# Cache-Control and TTL

Cache-Control is the HTTP response header that tells every cache along the path how, and for how long, a response may be stored and reused, and TTL (time to live) is the duration a cached copy is considered fresh. Together they form the freshness contract: the server states its rules, and browsers, CDNs, and proxies obey them. When the TTL expires, the copy becomes stale and the cache must either revalidate it with the origin or fetch a new one. Getting these directives right is the difference between a cache that helps and one that serves outdated content or never caches at all.

## Key Cache-Control directives

Cache-Control carries a set of directives that control storage and lifetime, and the most common ones are max-age, no-cache, no-store, public, and private. The max-age directive sets the TTL in seconds, so max-age=3600 means fresh for one hour. The public directive lets shared caches like CDNs store the response, while private restricts it to the user's browser, and no-store forbids caching entirely. Confusingly, no-cache does not mean do not cache: it means a cache may store the response but must revalidate with the origin before reusing it.

## TTL and freshness

TTL is the heart of caching freshness, because it sets how long a copy can be served before the cache must check whether it is still valid. A short TTL keeps data fresh but produces more origin traffic, while a long TTL maximizes hit rate but risks serving stale content after the source changes. The right value depends on how often the data changes and how much staleness users can tolerate: seconds for a live dashboard, hours for a product page, a year for a versioned image. A common pattern is to set very long TTLs on immutable, versioned assets and short TTLs on anything that mutates.

## Validators and revalidation

Validators let a cache check freshness cheaply without re-downloading the full body, using the ETag and Last-Modified response headers. An ETag is an opaque fingerprint of the content, and Last-Modified is a timestamp; the cache sends them back via If-None-Match or If-Modified-Since when revalidating. If the content has not changed, the origin replies with a tiny 304 Not Modified, and the cache reuses its stored copy with a refreshed lifetime. This makes stale-but-unchanged content nearly free to confirm, combining the safety of short TTLs with most of the savings of long ones.

## Stale-while-revalidate

The stale-while-revalidate directive lets a cache serve a slightly stale copy immediately while it refreshes the entry in the background. This removes the latency penalty that normally hits the unlucky request that arrives just after a TTL expires. You set it alongside max-age, as in max-age=60, stale-while-revalidate=300, meaning fresh for 60 seconds and servable while revalidating for another 300. It is one of the simplest ways to keep response times flat even as cache entries cycle through expiry.
