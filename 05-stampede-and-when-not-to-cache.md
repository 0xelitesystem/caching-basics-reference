# Stampede and When Not to Cache

A cache stampede is a failure mode where a popular cache entry expires and many concurrent requests all miss at once, hammering the origin as they each try to recompute the same value. Also called the thundering herd or dog-piling, it can flatten a database the instant a hot key expires, turning a caching layer into a liability under load. The mitigations all aim to ensure that only one request does the expensive work while the others wait or serve stale data. Beyond stampedes, there are cases where caching is simply the wrong tool, and recognizing them saves you from chasing correctness bugs.

## How stampedes happen

A stampede happens because expiration is a cliff: at the TTL boundary the entry flips from fresh to absent, and every in-flight request for that key misses simultaneously. If a popular key backed by a one-second query expires while a thousand requests per second are arriving, a thousand identical queries hit the database at once. The origin, sized to handle the cached hit rate, is suddenly slammed with traffic it never normally sees. The problem is worst for expensive computations and for keys with very high request rates, which are exactly the keys most worth caching.

## Mitigations

The core mitigation is to ensure only one request recomputes a value while everyone else waits or serves the old copy, and several techniques achieve this. A mutex or lock lets the first request on a miss acquire it, recompute, and repopulate while other requests block briefly or return stale data. The stale-while-revalidate approach serves the expired copy and refreshes in the background, so no request ever blocks on the origin. Probabilistic early expiration, where entries are refreshed slightly before their TTL with a random jitter, spreads recomputation out so keys do not all expire on the same tick.

## When not to cache

Caching is the wrong choice when serving stale data is unacceptable or when the data changes as fast as it is read, because then the cache adds complexity and bugs without saving work. Highly personalized or per-request data, such as a one-time checkout total or a security token, has a hit rate near zero and gains nothing from caching. Write-heavy data that changes on nearly every read forces constant invalidation, so the cache churns more than it serves. Values that must always be exactly current, like inventory counts during a flash sale or a financial balance mid-transaction, should be read straight from the source of truth.

## Cache correctly or not at all

A half-correct cache is more dangerous than no cache, because it serves wrong answers silently and confidently. Before adding a cache, decide on purpose what staleness window is acceptable, how entries get invalidated, and what happens when a hot key expires under load. If you cannot answer those three questions, you are not ready to cache that value yet. The discipline of treating freshness as an explicit contract, rather than a default you stumbled into, is what separates a cache that helps from one that quietly corrupts your data.
