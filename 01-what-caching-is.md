# What Caching Is

Caching is the practice of storing a copy of data or a computed result in a fast, nearby location so that repeated requests for the same thing can be served from the copy instead of recomputing or refetching it. The stored copy lives somewhere cheaper to reach than the original source: in memory instead of on disk, on disk instead of across a network, or near the user instead of in a distant data center. Caching works because real workloads are repetitive, the same items get requested over and over, so a small store of recent results can satisfy a large share of traffic. The price you pay is that the copy can drift out of date and that you spend memory holding it.

## Hits, misses, and hit rate

A cache hit is when a requested item is found in the cache and returned directly, and a cache miss is when it is absent and the system must fetch or compute the real value. The hit rate, the fraction of requests served as hits, is the single most important number for a cache: a 90 percent hit rate means only one request in ten reaches the slow source. Each miss usually costs more than no cache at all, because you pay the lookup, the original work, and the write back into the cache. The goal is to keep the working set, the items actually being requested, small enough to fit in the cache.

## Why caching pays off

Caching pays off when the cost of producing a value is much higher than the cost of storing and retrieving it, and when that value is requested often enough to amortize the storage. A database query that takes 200 milliseconds can be served from memory in under a millisecond, so caching it turns a bottleneck into a rounding error. Caches also shed load: every hit is a request the origin database or service never sees, which protects it during traffic spikes. The benefit scales with read-heavy access patterns, which is why content sites, product catalogs, and API responses are classic caching targets.

## What can be cached

Almost any deterministic or slow-changing result is a caching candidate, including HTML pages, API responses, database query results, rendered images, and computed aggregates. The best candidates are read often, written rarely, and tolerant of being slightly stale, such as a public product listing or a daily report. Poor candidates are values that are unique per request, change on every read, or must always reflect the exact current state, such as a bank account balance during a transfer. Choosing what to cache is mostly about judging how often the underlying data changes versus how often it is read.
