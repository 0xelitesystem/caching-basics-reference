# Cache Layers

Caching happens at multiple layers along a request path, and a single value can be cached at several of them at once: the browser, a CDN, the application, and the database. Each layer sits closer to or farther from the user, and each one that scores a hit prevents the request from traveling deeper into the stack. The layers nearest the user give the biggest latency win but the least control, while the layers nearest the data give the most control but save the least network distance. Understanding the layers helps you decide where a given value should be cached and how the layers interact.

## Browser cache

The browser cache stores responses on the user's own device so that repeat visits and navigations avoid the network entirely. It is controlled almost entirely by HTTP response headers (Cache-Control, ETag, Expires) that the server sends, and it is private to one user. This is the fastest possible cache because a hit involves no network at all, just a disk or memory read on the client. Its weakness is that you cannot reach into it to purge entries, so once a response is cached with a long lifetime, you must wait it out or change the URL.

## CDN and edge cache

A content delivery network caches responses at servers distributed geographically close to users, serving cached copies from the nearest edge location. CDNs are shared caches, so one user's miss that populates the edge benefits every later user in that region. They excel at static assets and cacheable API responses, absorbing huge traffic volumes that would otherwise hit your origin. Unlike the browser, most CDNs expose a purge API, so you can actively invalidate edge copies when content changes.

## Application cache

The application cache lives inside or beside your server process and stores computed results, session data, or query outputs, often in an in-memory store like Redis or Memcached. This layer is where you cache things that are too dynamic or user-specific for a CDN but still expensive to compute on every request. You control it completely: you decide the keys, the TTLs, and exactly when to delete or overwrite entries. The tradeoff is that it sits behind the network from the user's perspective, so it saves computation rather than round-trip distance.

## Database cache

Database caching covers the buffers and caches the database itself maintains, plus any result caches you layer on top of it. The database keeps frequently accessed pages and index blocks in a memory buffer pool so common queries avoid disk reads. Query result caches and materialized views can store the output of expensive aggregations so they are not recomputed on every call. This layer is the last line before raw storage, and tuning its memory often yields large gains without any application changes.
