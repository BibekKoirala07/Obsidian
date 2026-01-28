Love this approach. This is **exactly** how seniors learn:  
pick _one_ subsystem → exhaust it → then integrate it with everything else.

Below is a **deep, structured, “no-hand-waving” CDN syllabus**.  
You don’t need to learn it all at once — this is a **map**, not a to-do list for today.

Think of this as your **CDN bible** 📘

---

# 🧠 CDN — COMPLETE SYLLABUS (Senior-Level)

You can literally paste this into Obsidian and tick things off.

---

## I. CDN Foundations (Mental Model)

You must be able to explain CDN without naming a vendor.

- What problem CDNs solve
    
- Origin vs Edge vs Shield
    
- Push vs Pull CDN
    
- How CDNs evolved historically
    
- Why latency dominates web performance
    
- Physical distance vs network distance
    
- CDN ≠ cache only (routing + security + compute)
    

---

## II. Global Traffic Routing

How requests reach the “right” edge.

- DNS-based routing
    
- Anycast vs Unicast
    
- Geo-based routing
    
- Latency-based routing
    
- ISP peering & network topology
    
- DNS TTL tradeoffs
    
- Failover between edges
    
- Multi-CDN strategies (high-level)
    

---

## III. Edge Locations & Cache Architecture

What actually runs at an edge.

- What is an edge location?
    
- How many edges exist globally?
    
- Edge vs Regional Edge vs Shield (CloudFront model)
    
- Cache storage (memory vs disk)
    
- Eviction policies (LRU, LFU)
    
- Cache warming
    
- Cache isolation per edge
    

---

## IV. Cache Keys (THIS IS CRITICAL)

This is where most mistakes happen.

- What is a cache key?
    
- Default cache key behavior
    
- Path-based caching
    
- Query string caching
    
- Header-based caching
    
- Cookie-based caching
    
- Authorization header implications
    
- Cache key explosion
    
- Cache fragmentation problems
    
- Designing minimal cache keys
    

---

## V. HTTP Caching Semantics

CDNs obey HTTP — not magic.

- Cache-Control directives
    
    - public / private
        
    - max-age
        
    - s-maxage
        
    - no-store
        
    - no-cache
        
- Expires header
    
- ETag
    
- Last-Modified
    
- Conditional requests
    
- Revalidation vs refetch
    
- Stale-while-revalidate
    
- Stale-if-error
    

---

## VI. TTL & Invalidation Strategies

“How long should things live?”

- Default TTL
    
- Min / Max TTL
    
- Per-path TTL
    
- Hard vs soft invalidation
    
- Manual invalidation
    
- Event-driven invalidation
    
- Versioned assets (`main.a1b2.js`)
    
- Cache busting strategies
    
- Tradeoffs: freshness vs performance
    

---

## VII. CDN + Browsers (Often Ignored)

Two caches working together.

- Browser cache vs CDN cache
    
- Cache hierarchy
    
- How headers affect both
    
- Double caching pitfalls
    
- Private vs shared caches
    
- When browser cache beats CDN
    
- Hard reload vs soft reload
    

---

## VIII. Static Content Delivery

Classic CDN use case.

- HTML caching challenges
    
- CSS & JS caching
    
- Image optimization
    
- Font delivery
    
- Compression (Gzip, Brotli)
    
- HTTP/2 multiplexing impact
    
- Range requests for large files
    

---

## IX. CDN + APIs (Very Important)

This is where seniors shine.

- Should APIs be cached?
    
- Safe API caching patterns
    
- Public vs private APIs
    
- REST vs GraphQL caching
    
- Query-based API caching
    
- Cache by user vs cache by role
    
- Short-lived API caching (5–30s)
    
- Cache poisoning risks
    

---

## X. Authentication & Personalization

Why caching gets tricky.

- Authorization headers
    
- Cookie-based auth
    
- Session affinity problems
    
- Edge auth vs origin auth
    
- Signed URLs
    
- Signed cookies
    
- Token leakage prevention
    

---

## XI. CDN Security Capabilities

CDNs are security products too.

- DDoS protection
    
- Rate limiting
    
- Bot mitigation
    
- Web Application Firewall (WAF)
    
- OWASP Top 10 at the edge
    
- IP allow/deny lists
    
- Geo-blocking
    
- TLS termination
    

---

## XII. Edge Compute (Modern CDN)

Not just static files anymore.

- Edge functions (CloudFront Functions, Workers)
    
- Request/response manipulation
    
- Auth at edge
    
- Redirects & rewrites
    
- A/B testing at edge
    
- Limits & cold starts
    
- When NOT to use edge compute
    

---

## XIII. Performance Optimizations

Deep performance tuning.

- TCP slow start
    
- Connection reuse
    
- TLS session resumption
    
- HTTP/3 (QUIC)
    
- Prefetching & preloading
    
- Early hints (103)
    
- Image resizing at edge
    

---

## XIV. Failure Modes & Resilience

This is senior-only territory.

- Origin down scenarios
    
- Serving stale content
    
- Failover origins
    
- Shielding origins
    
- Thundering herd problem
    
- Retry storms
    
- CDN outages (yes, they happen)
    
- Graceful degradation strategies
    

---

## XV. Observability & Metrics

You can’t optimize what you can’t see.

- Cache hit ratio
    
- Origin request rate
    
- Edge latency
    
- Time to first byte (TTFB)
    
- Error rates
    
- Logs vs metrics
    
- Debug headers
    

---

## XVI. Cost & Economics

Very practical.

- Bandwidth pricing
    
- Cache hit vs cost
    
- Origin egress reduction
    
- Over-caching mistakes
    
- Invalidations cost
    
- Multi-CDN cost tradeoffs
    

---

## XVII. CDN + Frameworks

Real-world usage.

- CDN with React/Vite
    
- CDN with Next.js (SSR/SSG/ISR)
    
- CDN with NestJS APIs
    
- CDN with GraphQL
    
- CDN with WebSockets (limitations)
    
- CDN with file uploads
    

---

## XVIII. CDN Anti-Patterns

Learn by avoiding pain.

- Caching authenticated HTML
    
- Overusing cache keys
    
- Disabling cache accidentally
    
- Cache busting everything
    
- Treating CDN as magic
    
- Ignoring browser cache
    

---

## XIX. Vendor-Specific Mapping (LAST)

Only after concepts.

- CloudFront architecture
    
- Cloudflare architecture
    
- Akamai basics
    
- Fastly basics
    
- Key differences in philosophy
    

---

## XX. Interview & Real-World Questions

You should be able to answer:

- How do CDNs improve performance?
    
- How do you cache APIs safely?
    
- Why is cache invalidation hard?
    
- What happens on cache miss?
    
- How does CDN handle global traffic?
    
- What breaks CDN caching?
    

---

# 🧭 How to use this syllabus (important)

1. Pick **one section**
    
2. Ask me about **one bullet**
    
3. Build mental models + examples
    
4. Move to next bullet
    

No rushing. Depth > speed.

---

## Next step (your call)

Say something like:

- “Start with II. Global Traffic Routing”
    
- “Explain Cache Keys deeply”
    
- “Explain CDN + API caching with examples”
    
- “Explain Edge compute vs backend”
    

We’ll go **one brick at a time** 🧱