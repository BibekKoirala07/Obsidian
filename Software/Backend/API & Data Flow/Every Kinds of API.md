### Rest API
### Graphql
Graphql
understand relay and relay spec
It is a Query Language. It is not a database, rather a query language.

Microservice graphql is the greatest thing that has happened in an enterprise context. Consistency, robustness, responsibility.

|                                                     |                                                                                     |
| --------------------------------------------------- | ----------------------------------------------------------------------------------- |
| **sign around endpoints**                           | ➤ Design around **types and data relationships**                                    |
| 2. **One route = one job**                          | ➤ One query can fetch multiple related types at once (`user`, `posts`, `comments`)  |
| 3. **Return `{ success, message, data }`**          | ➤ Return just `data` (or add custom wrappers if needed)                             |
| 4. **Use HTTP status codes for logic**              | ➤ GraphQL always returns `200 OK` — errors go in the `errors` array                 |
| 5. **Paginate with offset/limit**                   | ➤ Prefer cursor-based pagination (`first`, `after`)                                 |
| 6. **Separate endpoints for nested resources**      | ➤ Use **nested queries** (`user.posts.comments`)                                    |
| 7. **Manually shape response objects**              | ➤ The **client decides** what fields it wants from the schema                       |
| 8. **Controller-centric thinking**                  | ➤ Move toward **resolver-per-type-field** or at least **resolver-per-root-field**   |
| 9. **Stateless "actions" (e.g., login, resetPwd)**  | ➤ Still use mutations, but often better as **typed inputs + typed outputs**         |
| 10. **Think in verbs: `/getUser` or `/createUser`** | ➤ GraphQL is about **declaring shape**, not actions: `query { user(id:1) { ... } }` |

- **Edge-node**
    
- **PageInfo pattern**
    
- **GraphQL pagination best practices**
- **Authorization**
    
- **GraphQL context usage**
    
- **Real production stuff**
- Lazy Loading and Eager Loading
###### ✅ Stage 1: Must
-Have Features (Core)

|Feature|Why it matters|
|---|---|
|🔐 **Authentication**|So only logged-in users can perform actions|
|🧪 **Validation**|To avoid bad inputs (name too short, etc.)|
|⚠️ **Error Handling**|Centralized structure like `success, message`|
|🔁 **Pagination**|Especially cursor-based for `posts`, `users`|
|🎯 **Input Types**|Cleanly typed `input {}` instead of raw args|

---

###### ✅ Stage 2: Important for Real Apps

| Feature                                | Why it matters                            |
| -------------------------------------- | ----------------------------------------- |
| 🙋‍♂️ **Me Query** (`me`)              | Get the currently logged-in user          |
| 🚫 **Authorization** (role, ownership) | Can only delete your own posts, etc.      |
| ⛔ **Rate limiting / throttling**       | Prevent abuse                             |
| 🔍 **Filtering & sorting**             | e.g., `posts(filter: { tag: "graphql" })` |

---

##### ✅ Stage 3: Nice-to-Have for Production Polish

|Feature|Why it matters|
|---|---|
|🛠️ **Custom Scalars** (e.g. `DateTime`)|Avoid using plain strings for dates|
|📊 **GraphQL Depth Limit / Complexity Limit**|Prevent DoS via deeply nested queries|
|📈 **Logging & Monitoring**|Trace slow queries|
|📦 **Modular Context** (e.g. current user, services)|Cleaner resolvers|

- Cursor-based pagination
    
- GraphQL subscriptions
    
- Dataloader batching
    
- Query depth limiting
    
- Federation (for microservices)
### Webhooks
### Web sockets
### gRPC
### RPC
### webRTC
### Streaming/SSE
### SOAP
### AMQP

Ah! Now I get exactly what you mean 😄 — you’re not asking for “variations of HTTP” like webhooks, SSE, or GraphQL. You want **fundamentally different types of APIs / communication mechanisms**—things that are **architecturally distinct**, not just flavors of HTTP.

Let’s clean it up properly. I’ll categorize **truly different kinds of APIs** by **how they communicate**:

---

## **1️⃣ Request-Response APIs (Synchronous)**

- **HTTP / REST**
    
    - Standard request → response over HTTP.
        
    - Stateless.
        
    - Example: `/users/123` → JSON.
        
- **gRPC / Thrift / Protobuf RPC**
    
    - Binary, contract-first RPC over HTTP/2.
        
    - Synchronous call like `function(params) → result`.
        
    - Faster than JSON/HTTP.
        

---

## **2️⃣ Streaming / Real-Time APIs**

- **WebSocket**
    
    - Full-duplex, persistent connection.
        
    - Client & server can push data at any time.
        
- **Server-Sent Events (SSE)**
    
    - One-way server → client streaming over HTTP.
        
- **gRPC Streaming**
    
    - Server streaming, client streaming, bidirectional streaming.
        

---

## **3️⃣ Message-Oriented / Asynchronous APIs**

- **AMQP (RabbitMQ)**
    
    - Queue-based asynchronous messaging.
        
    - Producer → broker → consumer.
        
- **Kafka (Event streaming)**
    
    - High-throughput, distributed streaming.
        
    - Event log pattern; consumers can replay history.
        
- **MQTT**
    
    - Lightweight pub/sub protocol for IoT devices.
        

---

## **4️⃣ File / Binary Transfer APIs**

- **SOAP**
    
    - XML-based RPC. Can include attachments.
        
- **FTP / SFTP APIs**
    
    - For file transfer over network, sometimes exposed as APIs.
        
- **gRPC with large data streaming**
    
    - For sending binary blobs efficiently.
        

---

## **5️⃣ Local / In-Memory APIs**

- **Shared Memory / In-Process APIs**
    
    - E.g., a Python service calling another object via function call.
        
- **DB-as-an-API**
    
    - E.g., Firebase Realtime Database, Supabase, or direct PostgreSQL queries via API layer.
        

---

## **6️⃣ Hardware / Low-Level APIs**

- **Serial / TCP / UDP APIs**
    
    - Devices talking over raw sockets or serial protocols.
        
- **IoT Protocols**
    
    - CoAP, Modbus, custom binary protocols.
        

---

### ✅ Key Idea

- **HTTP / REST / Webhooks / GraphQL / SSE** → all **HTTP-based**, same fundamental communication.
    
- **WebSocket, gRPC streaming, AMQP, Kafka** → **persistent or async communication**.
    
- **RPC with binary protocols** → **function-call style APIs over network**.
    
- **DB-as-an-API** → **direct storage-access APIs**.
    
- **IoT / hardware APIs** → **protocol-specific low-level APIs**.
    

---

If you want, I can make a **visual map** with **all fundamentally different API types**, **how they communicate**, and **when to use them**—no “HTTP variants” included. It’ll make your notes clean and interview-ready.

Do you want me to do that?


