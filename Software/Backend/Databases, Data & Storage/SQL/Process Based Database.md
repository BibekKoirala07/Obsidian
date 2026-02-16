###### 1️⃣ Process-based architecture (this already makes it different)

###### PostgreSQL is **process-based**, not thread-based

- Each client connection → **one OS process**
    
- The OS scheduler handles concurrency
    
- Shared memory is used for:
    
    - WAL buffers
        
    - Shared buffers (cache)
        
    - Locks
        

###### Why this matters

**Pros**

- Process crash ≠ entire DB crash
    
- Strong isolation between connections
    
- Easier correctness guarantees
    

**Cons**

- Higher memory per connection
    
- Needs connection pooling (PgBouncer)
    

🔹 **Contrast**

- MySQL (InnoDB): thread-based
    
- MongoDB: thread pool inside single process
    

Postgres chose **safety over raw throughput**.

That choice repeats everywhere.

PostgreSQL is a process-based database, not thread based(A real Linux process). Meaning, every one client connection equals to one OS process is used. Like when 100 clients are connected then 100 backend process are created. They all share shared memory, WAL buffers, Lock Tables, etc. Each process executes queries, manage transaction and can crash independently. 

The reason behind choosing PostgreSQL is that OS process isolation is rock-solid. It already gives you memory isolation, fault isolation, kernel-enforced safety, etc. Like if one backend segfaults, runs bad code or hits edge case then only that process(connection) dies. Thus DB keeps running. If it was thread based, then whole process could be contaminated thus killing huge amount of connection. Also it would produce hard-to-reproduce bugs and also global memory issues. PostgreSQL chose safety over density. And guess what process scales predictably also. 

Also process provides simpler mental model for concurrency. While thread share everything, process only share what we explicitly allow. This makes:

- Locking easier to reason about
- MVCC safer to implement
- Crashes less catastrophic

While there are tons of benefits of being process-based there are downsides also, Each process uses memory, spawns a process and OS Overhead. So We must use connection pooling, pg Bouncer exists and "Too many connection" is common PG error. 

when  MySQL is thread based and MongoDB is thread pool inside single process. Now you could argue that PostgreSQL needs higher memory per connection right ? 

It is a general purpose transaction data engine designed to protect correctness under concurrency. Pg is build for Long-term correctness under growth. 

Another thing is Pg never overwrites data in places. Ever.
Instead Pg keeps the old row(data) and new row(data) is inserted. And each row has `xmin` -> transaction that created it and `xmax` -> transaction that deleted/updated it. 

MVCC intuition - Time exists inside the database
Meaning different users can see different versions of same row at the same time and both are correct. That is it. That's MVCC.

Let's understand with example. Let say we have this connection from Nest to PostgreSQL 
```js
import {Client} from "pg" //  a piece of code that opens a TCP connection to PG

const client = new Client(config)
await client.connect() // this opens one TCP socket to Postgres 5432
```
This code when run in Nest tells that Nest app wants to establish TCP connection to PostgreSQL (`client.connect()`). When PG receives the connection, PG which is parent process(`postmaster`) spawns child process(which is backend process) which represent that connection by doing the `fork()` in Linux. In Windows the mechanism is different. That backend process is then dedicated to the app/TCP connection. One thing to care is that, It isn't the connection that is process but the pg backend that is process(We need to look from PG point to view to visualize this)
```
Node Process(Nest.js)
|
|   1 TCP Connection
v
Postgres backend process(1)
```
So we can see one client = One PostgreSQL backend process. What we need to care is that connection isn't the process. From PG POV, PG calls the Nest app a client. To server that client, PG creates One backend OS process. So
```
Node app --- TCP --- Postgres backend process
```
This process lives on DB Machine, is written in C, executes the SQL for that one client and dies when the client disconnects. 
```
                  ┌───────────────────┐
                  │   NestJS App      │
                  │   (Node.js)       │
                  │   1 OS Process    │
                  └───────────────────┘
                             │
                             │  TCP Connection (client.connect())
                             ▼
                  ┌───────────────────┐
                  │ Postgres Parent   │
                  │ Process (Postmaster)
                  │   Listens on 5432 │
                  └───────────────────┘
                             │
                             │ fork()
                             ▼
                  ┌───────────────────┐
                  │ Postgres Backend  │
                  │ Process  │
                  │ Dedicated to this │
                  │ connection/client │
                  └───────────────────┘
                             │
                             │ Executes SQL queries
                             ▼
                  ┌───────────────────┐
                  │   Database Storage│
                  │   WAL, Shared Mem │
                  └───────────────────┘

```

And Each backend process from PG also has private memory also. That private memory has 
```
  - work_mem (sorts, hashes, joins) // cound be around 8mb
  - maintenance_work_mem (VACUUM, CREATE INDEX)
  - Execution context and query state
```

With simple logic, we can see memory usage grows with number of connection. Increasing `max_connection` increase baseline RAM usage. 

Now let's move forward in that Nest example. Why do we need connection pool ? This is a conceptual question. 
Let's take the example of Mongoose
```ts
app.get("/c", () => {
	// DB call
})

app.get("/a", () => {
	// DB call
})

app.get("/b", () => {
	// DB call
})
```
So here let's say 3 request comes in at same time. Then, does all 3 request get executed parallel until it reaches async boundary. Then what is the problem ? that asynchronous request(DB call in problem). What if we have just one Pool(connection) ? All request will move parallely and which ever goes first to async boundary will get the opportunity to request to use that connection from pool  and it will use it(until now other's are waiting for connection from pool). once that request clear that async boundary(DB call), another request get that is stuck get's it. and so on. What if pool size was increased ? Then two async DB call can happen at same time right ? Same happens with Mongoose. The pool size is hidden from plain sight. You can explore this thing in mongoose docs. Same with PostgreSQL too. What if 200 request comes in and there is only one connection(`client.connect()`). Well then DB call would happen one by one but that is not what we want. So we need connection pool in PostgreSQL also. 

Now 
```ts
const pool = new Pool({ max: 5 })
```
It means 5 request can be sent concurrently to DB. If 3 request arrive then, they aren't waiting for DB access at all. So you may ask why not do `client.connect()` five times rather than pool. But think for a minute. How to know in runtime which client connection is free/idle and which is busy ? Do you handle every client disconnection everytime it happens ? Who handles queuing ? Who detects dead connection ? So if we need 50 connection, Will you do it manually ? Who does fault handling ?  Doesn't it make code complex ? Well Pool automatically does this things for you. Pool automatically handles queuing of idle connection, scaling, automatic reuse and other things. So Connection pool makes our work easier. 

But there is caveat ? Why not put Pool number to 10000 ? Now think for a minute. Each connection consumes 10 MB typically. Node.js can issue 10,000 concurrent request but can database handle it at a time ? Cuz resources are limited. It just increases memory consumption so why do it ? right ? why have so much context switching and process overhead ? 

Ideal number of pool connection. 
```
pool size = no of CPU cores times (2-4)

Adjust depending on query type:
- CPU-heavy queries → smaller pool
- I/O-heavy queries → slightly larger pool
```

Now we know per app there are about 5 backend connection from PG. But what if there are multiple app/microservices. Now we may have hundred to thousands of connection. Now, memory blow-up, crashes or slow queries may happen and CPU context switching happens. So even if we use pool in Node.js, the DB might see hundreds of connection. 

Here comes PgBouncer - It sits between the app and PostgreSQL
Node.js app connects to PgBouncer directly instead of PostgreSQL and then PgBouncer manages real backend connection to PostgreSQL. It is small server that sits between apps and SQL. It is a standalone process, not a part of Node.js or DB. And it manages limited number of backend connection to PG while exposing virtual connections to apps. It also queue request if all backend connection are busy. It makes apps feels like hundreds of connections and multiplexing them efficiently also. While PostgreSQL never sees more than N(let's say 50) connection. It 1. Acts as a **global connection pool** for that Postgres server.

Example of PgBouncer -> It is a different app. Now the Nest will connect to 6432(not 5432)

```text
[databases]
# database_name = host:port dbname user password
mydb = host=localhost port=5432 dbname=mydb user=myuser password=mypassword

[pgbouncer]
listen_addr = 127.0.0.1      # where apps connect
listen_port = 6432           # PgBouncer port (not 5432)
auth_type = md5              # password auth
auth_file = users.txt
pool_mode = transaction      # can be session/transaction/statement
max_client_conn = 100        # max virtual connections PgBouncer accepts
default_pool_size = 20       # max real backend connections to Postgres

```

Things to know
- Modes:
    1. **Session pooling**: 1 backend per virtual client session    
    2. **Transaction pooling**: 1 backend per transaction (most common, stateless)    
    3. **Statement pooling**: 1 backend per statement (extreme throughput)

###### 1️⃣ What PostgreSQL actually is

Postgres is:

- A **process-based database**
    
- With:
    
    - MVCC (Multi-Version Concurrency Control)
        
    - WAL (Write-Ahead Log)
        
    - Cost-based query planner
        
    - Real transactions
        

This means:

- Reads don’t block writes
    
- Writes don’t block reads
    
- Every row has versions


##### 🔹 Implication 2: Predictable failure modes

In Postgres:

- One query crashing ≠ database crashing
    
- One user misbehaving ≠ system outage
    

This is huge in production.

Mongo / thread-based systems:

- Bugs can cascade
    
- Memory corruption can kill the server
    

Postgres fails **locally**, not globally.

---

###### 🔹 Implication 3: OS scheduling matters

Because Postgres relies on the OS:

- CPU scheduling is fair
    
- Context switching is predictable
    
- Kernel handles starvation
    

This is why Postgres:

- Behaves well under load
    
- Degrades gracefully
    
- Rarely “locks up” completely
##### Reason 2: Shared memory + processes scale predictably

Postgres uses:

- Processes for execution
    
- Shared memory for coordination
    

This means:

- Less complex locking logic
    
- Fewer race conditions
    
- Easier correctness proofs
    

Correctness > cleverness.

---

##### Reason 3: Simpler mental model for concurrency

Threads share _everything_.

Processes share:

- Only what you explicitly allow
    

This makes:

- Locking easier to reason about
    
- MVCC safer to implement
    
- Crashes less catastrophic
    

Remember:

> Databases fail in the worst possible moments.

Postgres is designed for _that_ moment.

##### Why this matters in real life (not theory)

##### Scenario: traffic spike

**Thread-based DB**

- Threads fight over memory
    
- One bad query can stall others
    
- Global locks get contended
    

**Postgres**

- Each backend progresses independently
    
- Slow queries don’t poison others
    
- OS handles fairness
    

This is why Postgres is trusted for:

- Financial systems
    
- Government systems
    
- Large SaaS platforms

###### 5️⃣ Why not everyone chooses this design?

Because it has tradeoffs.
##### Downsides of process-based design

- Higher memory usage per connection
    
- Lower raw connection count
    
- Needs external pooling
    
- Not ideal for serverless without pooling
    

MongoDB optimized for:

- Developer velocity
    
- Flexible schemas
    
- High connection counts
    

Postgres optimized for:

- Correctness
    
- Isolation
    
- Long-running systems
    

Different goals.

#### 6️⃣ Why _you_, personally, should care

Because this affects: