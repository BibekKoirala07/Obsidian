### Origin
Origin = protocols + host + port
Suppose the origin is `https://api.example.com`. Here `https` is protocol and `api.example.com` is host and `443` is port(which isn't visible but it is default for https). And yes `www` is also subdomain and later it became optional. It is upto website owner what to put as canonical domain(main) whether it is `example.com` or `www.example.com`. The web doesn't require `www` subdomain anymore as web service is assumed to be default. It required at past cuz in past we used to have `www.example.com`, `ftp.example.com`, `mail.example.com` and more. So services were different.

`.com` or `.io` or `.dev` are called Top level domain or last part of domain. It is top level because it is highest level. Like the address in internet starts from this TLD. Everything like `google.com` starts after that. They don't affect the speed at all. They work the same despite it not feeling right. However some new TLD like `.app` or `.dev` requires `https` and not `http`.

However use naked domain as it is better for marketing.

Here is amazing realization.
`https://media.nepal.gov.np` Here `gov` is main domain. and `nepal` is subdomain. Not other thing. It was huge realization at the time of finding it out. However for some website which have `.uk` as TLD, we need to have second level Domain. so `.co.uk` or `.gov.uk` is compulsory.

### IP Address
Unique address associated to every devices. IP defines the machines and PORT defines the service on that machine.

### SSH
It let us securely log to another computer over a network using IP Address.
ssh root@182.93.94.210 (ssh username@IP()) or @nepatronix.org tyo ip chai hamro vps ko ho

password Nepa@tronix

git clone gitclone@githubsecretclassicone  like git clone https://ghp_fjdfjdj@github.com/username/project.git

npm i

// upto here process for all things like next, react,js and node.js are same 

npm i (chai yedi change gareko ho bhane)
npm run build garni

build bhaisakepaxi pm2 restart

and pm 2 bata

domain nabhai ssl hunxa

###### Node
git push garni

cd garera tyo project ma jani
naya project lai root level ma git pull
npm i
pm 2 restart id()

there is option to `pm2 reset all`

Baki sab yestai ho ani
pm2 start node index.js  
yespaxi run hunxa
pm2 status le table herna milxa
tyo lai end garna lai pm2 stop id(yo dinxa) or pm2 delete id() avoid delete

###### Vps ma domain kasari milaune
###### Cpanel


###### Query
Domain ma gayera active bhanni ma gayera addons bhanni section hunxa and tesma DN Host Record Management hunxa tyo chai ani tesma add gare




---
### Internet Fundamentals
**Goal:** Learn and _retain_ key Internet concepts that help you debug better, build more secure and reliable apps, and speak confidently with seniors or mentors.

---

## 🔁 FORMAT FOR EACH WEEK:

1. **Concepts to Learn** (reading)
    
2. **Command-line / DevTools Tasks** (application)
    
3. **Mini Real-world Exercises** (practice)
    

---

### ✅ Week 1: How the Internet Works

**Concepts**

- What is a client & server?
    
- IP, DNS, Port
    
- What happens when you type `google.com`
    
- TCP/IP basics
    

**Tasks**

- Use `ping`, `traceroute`, `nslookup`, `dig`
    
- Look up DNS records for `google.com`
    
- Explore IP/hostname via `ip a` and `hostname`
    

**Mini Project**

- Write a markdown note: “What happens when I visit https://youtube.com”
    
- Show each step: DNS → IP → TCP → HTTP → Response
    

---

### ✅ Week 2: HTTP, Status Codes, Headers

**Concepts**

- HTTP request structure
    
- Methods: GET, POST, PUT, DELETE, PATCH
    
- Status codes: 200, 201, 404, 500
    
- Common headers: `Content-Type`, `Authorization`, `Origin`, `User-Agent`
    

**Tasks**

- Use browser DevTools → Network tab
    
- Use `curl -I https://example.com` to view headers
    
- Try `curl -X POST https://reqres.in/api/users -d '{"name":"test"}'`
    

**Mini Project**

- Call a public API using `curl` and analyze the response
    
- Write a note describing the full HTTP request & response
    

---

### ✅ Week 3: Origin, CORS & Storage

**Concepts**

- URL breakdown (protocol, host, port, path)
    
- What is “Origin” (scheme + host + port)
    
- Same-Origin Policy
    
- CORS: Why it's needed
    
- Cookies vs `localStorage` vs `sessionStorage`
    

**Tasks**

- Open DevTools → Application tab → Play with `localStorage`
    
- Use JS Console to inspect: `location.origin`, `document.domain`
    
- Observe a CORS error by calling an API from wrong origin
    

**Mini Project**

- Build a small HTML + JS file → call an API → fix CORS with a proxy (like cors-anywhere)
    
- Make a table comparing cookies, localStorage, sessionStorage
    

---

### ✅ Week 4: Authentication & Sessions

**Concepts**

- How login works on the web
    
- Session-based vs Token-based auth
    
- HTTP-only cookies
    
- JWT: What it looks like, how it's used
    
- Basic OAuth2 flow
    

**Tasks**

- Decode a JWT from a public API using [https://jwt.io](https://jwt.io)
    
- Simulate login via local API: set cookie or store token in localStorage
    
- Use `curl` with `Authorization` header
    

**Mini Project**

- Make a basic login/logout flow with cookie or JWT storage
    
- Write a note: “How a secure login flow works step-by-step”
    

---

### ✅ Week 5: DevTools + Debugging Deep Dive

**Concepts**

- Browser cache, HTTP caching headers
    
- Inspecting cookies, localStorage
    
- Tracking 404/500 errors in Network tab
    
- Analyzing load time (TTFB, waterfall)
    

**Tasks**

- Use Network tab to:
    
    - Filter by request type (XHR, JS, Img)
        
    - Inspect headers, size, cache status
        
- View cache-control headers in DevTools
    
- Use `curl -v` to follow redirects
    

**Mini Project**

- Debug a slow-loading page → write steps: DNS, cache, TLS, etc.
    
- Compare behavior with cache disabled
    

---

### ✅ Week 6: Security Concepts for Developers

**Concepts**

- HTTPS/TLS & certificates
    
- CSRF: how it works
    
- XSS: how it happens, how to prevent
    
- CORS again (with real examples)
    
- Basic secure headers: `Strict-Transport-Security`, `X-Frame-Options`
    

**Tasks**

- Visit a secure site → click 🔒 icon → view certificate
    
- Simulate an XSS input in a dummy form (`<script>alert(1)</script>`)
    
- Observe behavior of `SameSite` cookies
    

**Mini Project**

- Build a secure contact form:
    
    - Sanitize inputs
        
    - Use HTTPS
        
    - Use CORS policy
        

---

## 📘 BONUS TIP — Your Internet Knowledge Journal

- Create a `web-fundamentals.md`
    
- Each week, update:
    
    - Definitions (e.g., "What is origin?")
        
    - Common headers seen
        
    - Common issues you debugged
        
- Build your **mental cheatsheet** over time

Alright, I’ll be straight with you. If you say “I know the internet” but you don’t know **what’s actually happening under the hood**, you’re bluffing. As a backend dev, this stuff is non-negotiable. Not optional. Not “nice to have”.

Below is a **proper, no-BS Internet syllabus** — from wire → browser → backend → scale → security. Treat it like a map. You don’t master everything at once, but you **must know what exists**.

---

## 0. Foundations (don’t skip this, ever)

If this is weak, everything else collapses.

- What is the Internet vs Web
    
- Client–Server model
    
- Peer-to-peer vs centralized systems
    
- Packets, frames, segments (big picture)
    
- Bandwidth vs latency vs throughput
    
- What happens when a network is “slow”
    

---

## 1. Networking Basics (absolute core)

You should be able to **explain this without slides**.

- OSI Model (7 layers) — purpose, not memorization
    
- TCP/IP Model (real-world model)
    
- What each layer is responsible for
    
- Encapsulation & decapsulation
    
- MAC address vs IP address
    
- IPv4 vs IPv6 (why IPv6 even exists)
    

---

## 2. Physical → Data Link Layer

Yes, backend devs still need this.

- Ethernet
    
- Switches vs hubs
    
- ARP (how IP → MAC actually works)
    
- Collision domains vs broadcast domains
    
- MTU (why packets sometimes break)
    

---

## 3. Network Layer (IP & Routing)

This is where the “internet” starts to feel real.

- IP addressing & subnetting (CIDR)
    
- Public IP vs Private IP
    
- NAT (very important)
    
- Routing basics
    
- Routers vs switches
    
- ICMP (ping, traceroute)
    

---

## 4. Transport Layer (VERY IMPORTANT)

Most people hand-wave this. Don’t.

### TCP

- 3-way handshake
    
- Connection termination
    
- Reliability, ordering, retransmission
    
- Flow control vs congestion control
    
- Head-of-line blocking
    
- Keep-alive
    

### UDP

- When UDP is better than TCP
    
- Real-world uses (DNS, streaming, gaming)
    

### Comparison

- TCP vs UDP (real tradeoffs, not textbook)
    

---

## 5. DNS (people underestimate this)

DNS is **not just “domain → IP”**.

- Recursive vs authoritative DNS
    
- DNS records: A, AAAA, CNAME, MX, TXT, NS
    
- DNS caching & TTL
    
- What happens when DNS fails
    
- Why DNS is a bottleneck sometimes
    

---

## 6. HTTP / HTTPS (your bread and butter)

If you touch backend and don’t know this deeply, it shows.

### HTTP Basics

- Statelessness
    
- Request–response lifecycle
    
- Methods: GET, POST, PUT, PATCH, DELETE
    
- Idempotency (huge for backend)
    
- Headers (important ones only)
    
- Status codes (2xx, 3xx, 4xx, 5xx)
    

### HTTP Versions

- HTTP/1.1 (keep-alive, limitations)
    
- HTTP/2 (multiplexing, headers compression)
    
- HTTP/3 (QUIC, UDP-based)
    

### HTTPS

- TLS/SSL handshake
    
- Certificates (CA, public/private key)
    
- Why HTTPS is mandatory
    
- MITM attacks (basic idea)
    

---

## 7. Browser Internals (very underrated)

Backend devs ignore this — they shouldn’t.

- What happens when you type a URL in browser
    
- Browser rendering pipeline
    
- Caching (memory, disk, HTTP cache)
    
- Cookies vs LocalStorage vs SessionStorage
    
- Same-Origin Policy
    
- CORS (deeply understand this)
    

---

## 8. Proxies, Gateways & Firewalls

Critical for real systems.

- Forward proxy vs reverse proxy
    
- Load balancer basics
    
- API Gateway
    
- WAF (Web Application Firewall)
    
- Nginx / HAProxy (conceptual role)
    

---

## 9. Authentication & Authorization (Internet reality)

Security is not optional.

- Authentication vs Authorization
    
- Sessions vs Tokens
    
- Cookies-based auth
    
- JWT (pros, cons, lies people tell)
    
- OAuth 2.0 (flows conceptually)
    
- OpenID Connect (why it exists)
    

---

## 10. Caching & Performance

This separates juniors from seniors.

- Client-side vs server-side caching
    
- HTTP caching headers
    
- CDN (how it really works)
    
- Cache invalidation (hardest problem, yes)
    
- Latency optimization basics
    

---

## 11. WebSockets & Real-Time Communication

Not everything is request–response.

- Polling vs long polling
    
- WebSockets
    
- Server-Sent Events (SSE)
    
- When NOT to use WebSockets
    

---

## 12. Security Fundamentals (you must know this)

No excuses.

- Common attacks:
    
    - XSS
        
    - CSRF
        
    - SQL Injection
        
    - SSRF
        
    - DDoS
        
- Rate limiting
    
- CORS misuse
    
- Secure headers (HSTS, CSP basics)
    
- Hashing vs encryption
    

---

## 13. Email & Background Internet Stuff

People forget this exists.

- SMTP basics
    
- Why emails go to spam
    
- SPF, DKIM, DMARC (concept level)
    
- Webhooks (internet-to-internet communication)
    

---

## 14. Observability & Debugging

Real engineers debug networks.

- Logs vs metrics vs traces
    
- HTTP debugging
    
- Wireshark (you’ve touched it — good)
    
- curl, ping, traceroute, nslookup, dig
    

---

## 15. Scale & Reliability (Internet at scale)

This is where money is made.

- Horizontal vs vertical scaling
    
- Stateless vs stateful services
    
- Load balancing strategies
    
- Fault tolerance
    
- Rate limiting & throttling
    
- Backpressure
    

---

## 16. Modern Internet Architecture (high-level)

Don’t go deep, but know it exists.

- Microservices vs monolith
    
- Service discovery
    
- REST vs GraphQL vs gRPC
    
- Event-driven systems
    
- Message queues (conceptual role)
    

---

## How to approach this (real talk)

- Don’t “study” this like college.
    
- Pick **one layer at a time**.
    
- Always ask:  
    **“What problem does this exist to solve?”**
    
- Tie everything back to **real requests hitting your NestJS backend**.
    

If you want, next we can:

- Convert this into a **90-day Internet mastery plan**
    
- Or map this **directly to backend interview expectations**
    
- Or go layer-by-layer and **destroy illusions** (my favorite 😈)
    

Your move.
