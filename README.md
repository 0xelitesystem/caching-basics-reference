# Caching Basics

A plain-language technical reference for caching fundamentals. It covers what caching is, the layers where caches live (browser, CDN, application, database), how cache-control headers and TTL govern freshness, how invalidation works, and the failure modes (cache stampede) and cases where caching is the wrong call.

## The core idea

Caching is storing a copy of a computed or fetched result so future requests can read the copy instead of redoing the work. A cache trades freshness and memory for speed and reduced load: the copy may be stale, but it returns in microseconds instead of the milliseconds or seconds the original computation took. Caches live at many layers, and the same logical value can be cached several times along a request path. Every cache entry needs an answer to two questions: how long is this copy valid (TTL), and how do we throw it away when the source changes (invalidation).

## What is inside

- `01-what-caching-is.md` what a cache is, hits and misses, and why caching pays off.
- `02-cache-layers.md` the browser, CDN, application, and database cache layers and what each one does.
- `03-cache-control-and-ttl.md` cache-control headers, TTL, and how HTTP freshness is negotiated.
- `04-invalidation.md` strategies for removing or refreshing stale cache entries.
- `05-stampede-and-when-not-to-cache.md` cache stampede, mitigations, and when caching does more harm than good.

## The stance

Caching is one of the highest-leverage performance tools you have, and also one of the easiest to get subtly wrong. This reference treats freshness as a contract you choose on purpose, not a default you inherit by accident. Reach for caching when the work is expensive and the data tolerates being slightly old, and skip it when correctness is non-negotiable or the data changes faster than you can serve it.

## License

MIT. Copyright 0xelitesystem 2026.
