# Invalidation

Cache invalidation is the act of removing or refreshing a cached entry once the underlying data has changed, so that stale copies stop being served. It is famously one of the hardest problems in computing because a cache, by design, does not know when its source has changed unless something tells it. The two broad approaches are passive expiration, where entries simply age out by TTL, and active invalidation, where your code or a CDN purge explicitly removes entries when a write happens. Choosing between them is a tradeoff between simplicity and how quickly fresh data must appear.

## Time-based expiration

Time-based expiration is the simplest invalidation strategy: every entry carries a TTL and is discarded once it ages past it. It requires no coordination between the writer of the data and the cache, which makes it robust and easy to reason about. The cost is a built-in staleness window equal to the TTL, during which the cache may serve data that has already changed at the source. This approach is ideal when bounded staleness is acceptable and a tighter window can simply be bought by shortening the TTL.

## Event-based invalidation

Event-based invalidation actively removes or updates a cache entry at the moment the underlying data changes, usually triggered by the same code path that performs the write. When a record is updated, the application deletes the matching cache key (or issues a CDN purge) so the next read repopulates it from the source. This gives near-instant freshness but demands that every write path knows which cache keys it affects, which is easy to get wrong as a system grows. Missed invalidations leave stale entries that linger until their TTL expires, which is why a backstop TTL is wise even with event-based purging.

## Key design and cache-aside

How you name cache keys largely determines how cleanly you can invalidate, so keys should map predictably to the data they hold. The common cache-aside pattern has the application check the cache, fall back to the source on a miss, and write the result back under a deterministic key. On a write, the same deterministic key lets you delete exactly the affected entry without guessing. Grouping related entries under shared prefixes or tags lets you invalidate a whole set at once, which matters when one change affects many derived views.

## Versioned keys

Versioning sidesteps invalidation entirely by baking a version or content hash into the cache key or URL, so new data lives at a new key. When the data changes, you write under a fresh key and old entries are never read again, aging out passively without any purge. This is the standard approach for static assets, where filenames like app.4f3a.js let you set effectively infinite TTLs safely. The cost is that you must propagate the new key everywhere it is referenced, but it eliminates a whole class of stale-content bugs.
