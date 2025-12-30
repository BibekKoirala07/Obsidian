Here we will learn about PostgreSQL.

A general-purpose transactional data engine designed to protect correctness under concurrency.
### Process-based Database
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
### MVCC(Multi-Version Concurrency Control)
Let's learn with examples. Let's say we have two Node clients connection to PG
```js
Node Client A → Backend Process #1
Node Client B → Backend Process #2
```
And Both want to read/write the same row at the same time. Well it is a problem now. It can be solved using MVCC. 
Every row in PG can have multiple version at the same time. When we update a row, PG creates a new version instead of overwriting the old one immediately. When we read the row, PG shows the version valid of our transaction. 
Let's say we have row in table
```
id | name | age | version
1  | John | 23  | v1
```
Now Client A want to update the age to 24. PG does this
```
id | name | age | version
1  | John | 23  | v1   <- old version, still readable
1  | John | 24  | v2   <- new version, for Client A's transaction
```
Meanwhile Client B can still read `v1`, because that was valid when it started its transaction( reading process in this context, transaction just means sequence of operations that PG treats as one atomic unit). So the main philosophy is reads don't block writes and writes don't block reads. 

So, Old versions stick around until PG cleans them(VACUUM).

### WAL - crash proofing
What if Client A sends an update ? The backend process executes it in memory and writes it to disk at the same time. But what if PG crashes in the middle of writing the change to disk ? That is seriously bad. 

So before changing the actual table on disk, PG writes the change to the separate log file(WAL). Only after the WAL is safely written, PG applies the changes to the table. If PG crashes, it replays the WAL to recover all committed changes. It is like journal or notebook. 

And every update, insert, delete and DDL operation is logged in WAL.

Let's say PG crashes before applying change to table after writing in WAL .On restart, PG reads the WAL and re-applies all committed changes to table. This is how PG guarantees durability(the D in ACID). If PG shuts down cleanly the WAL replay is usually not needed. 

### Transactions
A sequence of SQL operation that PG treats as one atomic unit. If all operation succeed -> `COMMIT`. If not `ROLLBACK`.

MVCC + WAL + backend process all exists to make transaction possible, safe and fast. 

#### Step-by-step Explanation

#### 1️⃣ Node Client starts a transaction

`BEGIN;`

- Node client tells backend: **“Start a transaction snapshot for me”**
    
- Backend process now tracks **all queries in this transaction**
    
- **MVCC snapshot** is taken → defines which row versions this transaction can see
    

---

#### 2️⃣ Queries are executed

`UPDATE users SET balance = balance - 100 WHERE id = 1; UPDATE users SET balance = balance + 100 WHERE id = 2;`

- Backend creates **new row versions** (MVCC) for updates
    
- Writes **all changes to WAL first**
    
- These changes are **not yet visible to other transactions**
    

---

#### 3️⃣ COMMIT or ROLLBACK

- **COMMIT**
    
    - Backend applies changes to actual table on disk
        
    - Old row versions can be cleaned later by VACUUM
        
    - Changes become visible to other clients
        
- **ROLLBACK**
    
    - Backend discards uncommitted row versions
        
    - WAL changes from this transaction are ignored
        
    - Table remains exactly as it was at the BEGIN
        

---

#### 4️⃣ Why this is important

- Ensures **atomicity** → either all updates succeed or none
    
- Ensures **isolation** → each transaction sees a consistent snapshot
    
- Works **with MVCC** → concurrent transactions don’t block each other
    
- Works **with WAL** → ensures durability even if Postgres crashes
    


If you like, the **next baby step** after Transactions is **tying it all together**:

- One Node client → backend → MVCC → WAL → Transactions
    
- **Then you can safely run SQL queries with full understanding of what Postgres does behind the scenes.**

### Shared Memory + Co ordination
As we all know PostgreSQL is process based. Which means each client gets it own backend process. Each backend process has its own memory(which know its own transaction, row versions, WAL writes). So each process are independent. So how do independent process know about Which rows are locked ? Which transaction are running ? How to maintain MVCC visibility ? 

A special memory area in PostgreSQL, visible to all backend processes. Like a common whiteboard that all backend can read/write safely. 

Now you may ask why do we need to lock rows right ? But imagine two transactions trying to update the same row. We need some mechanism to tell that "don't write this row yet!". 

It is central Brain. 

### Cost Based Query Planner
Let's say we have some query like `SElECT * FROM users WHERE age > 30`. Now we can fetch the data in many different ways. And Not all ways are equally fast. Like PG can scan the entire table. Use an index or Combine multiple tables via JOINs in different orders. The query planner decides the fastest way based on statistics and costs. 

When we are talking about cost-based ? Means PG assign a cost to each possible plan. How many rows it expects to scan. How much CPU/memory it will use, Disk I/O. Then it will picks the plan with lowest cost. It is very good at picking efficient plan automatically. SQL = `What you want`, Planner = `How Postgres will get it`.  At last, It is more about How PostgreSQL will run queries. 
### Installation
At minimum, we must install 3 components. They are
- PostgreSQL Server(postgre/postmaster). The old name is `postmaster`. It is still used conceptually. It listens on port `5432` also spawns backend processes on demand `fork()`. and also manages WAL, Shared buffers, Locks, Transaction, Query execution. Without this nothing works. 
- Client tools(psql, pg_dump, etc.)
- Data directory(where actual DB file live), PG doesn't store data inside the binary. It stores everything in a data directory often called `PGDATA`. Inside this directory, there are table files, Index files, WAL Logs, System catalogs, Configuration state. It is situated typically in `/var/lib/postgresql/v/main`. If this directory is deleted -> database is gone. 

Everything else is just add-ons or optional

PG uses plain text config files 
`postgresql.conf` -> controls port, memory, max_connections, WAL settings, Logging
`pg_hba.conf` -> It controls who can connect and how. 

And PG has its own users. They live inside PostgreSQL. Always remember that OS users != PG users. 

Among some optional tools some are frequently used like
- pgAdmin -> GUI for PostgreSQL
- Backups(`pg_dump) -> Safety
- Monitoring -> Production

Here’s the **correct next order**, not random tutorials:

1. PostgreSQL **roles, databases, schemas**
    
2. What happens when you run a SQL query internally
    
3. Transactions (`BEGIN / COMMIT / ROLLBACK`)
    
4. Indexes (B-tree, why they matter)
    
5. Query planner (EXPLAIN)
    
6. Locks & isolation levels
    
7. When PgBouncer breaks things (prepared statements, session state)
#### 🔟 What is REQUIRED vs OPTIONAL

### Authentication and Authorization
For PostgreSQL, it decides "Who are you ? " and "What can you do?". PG protects four main things. They are
- the server(Who can connect)
- A database
- Objects inside a database like table, views, functions, schemas
- and actions like SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, etc. 
From common sense we can know that PG must answer two question for every connection and query. They are "Who are you ? " and 'What are you allowed to do ? ' By simple logic, Authentication and Authorization. 

==There is no real difference between users and roles==. Historically there were users and groups. Modern PG unified everything under roles. 

PG just has exactly one concepts for identity: ==roles==. A role is just a record with some flags.
Two flags matter right now. 
- **LOGIN** -> Can this role connect to the DB ? 
- **PRIVILEGES** -> What can this role do ? 

So Let's say we have first role with `LOGIN=TRUE`(It is called user). It means that the role can communicate with PG. For example
```
ROLE: app
LOGIN: YES
PRIVILEGES: none
```
Now let's say we have second role with`LOGIN=FALSE`(It is called group). It means that the role can't connect at all. It can only hold permissions which is used by roles with `LOGIN=TRUE`.
For example:
```
ROLE: read
LOGIN: NO
PRIVILEGES:
	- SELECT on users
	- SELECT on comments
```

And the main thing is in PG, one role can inherit another role. It is the gist. 
So, if `app` is inheriting `read`, you can act like the permission of `read` is also being checked. 

So, first client comes in as `app`. PG looks for `LOGIN` whether it is `TRUE` or `FALSE`. If that value is negative, query doesn't move forward. But if that value is positive, then PG looks which permission(group) does that `app` has. but before looking for group, It does look for direct permission if that `app` has it or not. If not it inherit from `read`. And does that permission exists on `read`(group). And it does. so query is allowed. 

And one thing to realise is Authentication happens at connection time
It users password, certificates, OS users, trust and is controlled by `pg_hba.conf`. When client connects to PostgreSQL
```
Client -> TCP -> PG
```
First PG checks `pg_hba.conf` and match connection type(local/host), database, user and client IP. It decides ==auth method==, verify credentials, and thus allow or reject connection. Let's say for example, we have `pg_hba.conf` 
```
host mydb app 0.0.0.0/0 md5
```
host - > TCP connection
mydb -> database name
app -> POSTGRESQL role
0.0.0.0/0 -> any IP
md5 -> password auth

If this blocks the connection, then nothing else matter. 

For common auth methods, we have
trust(no password), md5(password), scram-sha-256(mosts popular, modern secure password), peer(OS user), cert(TLS certificate)

and Authorization happens after connection. It checks roles and privileges and is controlled by `GRANT/REVOKE`. PostgreSQL objects live inside schemas:
```
database
|--schema public
|   |----table users
|   |----table posts
|--schema private
```
We need permission on schema and permission on object inside it to get it. 

And Superusers can bypass all permission checks, can read any data and can change system. It shouldn't be used. 
#### Why Postgres does this (ONE reason only, for now)

Because databases:

- Do heavy CPU work
    
- Do blocking I/O
    
- Must isolate failures
    
- Must keep correctness
    

Threads make this _much harder_.

Processes make this **boringly safe**.
## One sentence to lock in

> **In PostgreSQL, one application-level client connection equals one dedicated database backend process.**


### 🔹 Implication 2: Predictable failure modes

In Postgres:

- One query crashing ≠ database crashing
    
- One user misbehaving ≠ system outage
    

This is huge in production.

Mongo / thread-based systems:

- Bugs can cascade
    
- Memory corruption can kill the server
    

Postgres fails **locally**, not globally.

---

### 🔹 Implication 3: OS scheduling matters

Because Postgres relies on the OS:

- CPU scheduling is fair
    
- Context switching is predictable
    
- Kernel handles starvation
    

This is why Postgres:

- Behaves well under load
    
- Degrades gracefully
    
- Rarely “locks up” completely
### Reason 2: Shared memory + processes scale predictably

Postgres uses:

- Processes for execution
    
- Shared memory for coordination
    

This means:

- Less complex locking logic
    
- Fewer race conditions
    
- Easier correctness proofs
    

Correctness > cleverness.

---

### Reason 3: Simpler mental model for concurrency

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

## Why this matters in real life (not theory)

### Scenario: traffic spike

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

## 5️⃣ Why not everyone chooses this design?

Because it has tradeoffs.

### Downsides of process-based design

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

## 6️⃣ Why _you_, personally, should care

Because this affects:

### Architecture decisions

- How you deploy
    
- How you scale
    
- How you pool connections
    

### Performance reasoning

- Why connection storms kill Postgres
    
- Why pooling fixes it
    
- Why fewer connections can be faster

## 7️⃣ How this ties into MVCC, WAL, transactions

This design enables:

- MVCC without global locks
    
- WAL safety without blocking reads
    
- Strong isolation guarantees
    

These aren’t independent features.  
They **require** this architecture.

## One sentence to remember forever

> **PostgreSQL is built to survive bad code, bad queries, and bad days — by isolating work at the OS level.**

That’s the bird’s-eye view.


## 3️⃣ Concurrency is normal, not exceptional

Postgres assumes:

- Hundreds of users
    
- Thousands of queries
    
- All at the same time
    
- On the same data
    

So it is designed for **conflict**, not avoidance.

## Data integrity is a _first-class feature_

In PostgreSQL:

- `NOT NULL` is not decoration
    
- `FOREIGN KEY` is not optional
    
- `UNIQUE` is not just an index
    

They are **rules of reality**.

### This leads to:

- Snapshot isolation
    
- Transaction boundaries
    
- Explicit locking (when needed)
    

If you think in “one request at a time”, you will design bad schemas.

## Storage is not the same as memory

This sounds obvious — but people forget it.

Postgres constantly balances:

- Disk I/O
    
- Memory cache
    
- WAL writes
    
- Background cleanup
    

### Why this matters

- Indexes are not free
    
- Updates are expensive
    
- Deletes don’t delete immediately
    
- VACUUM exists for a reason
    

This explains _performance behavior_ later.

## 6️⃣ The database plans queries, not you

You don’t “tell” Postgres how to run a query.

You **describe what you want**.

Postgres decides:

- Join order
- Scan type
    
- Index usage
    

### Your job is to:

- Model data correctly
    
- Create correct indexes
    
- Write clear queries
    

Trying to micro-control execution is a junior mistake.

## 7️⃣ Transactions are the unit of truth

In PostgreSQL:

- A single query is already a transaction
    
- A transaction defines:
    
    - Visibility
        
    - Atomicity
        
    - Isolation
        

### This means

- Partial failure is impossible
    
- Either reality changes, or it doesn’t
    
- There is no “half-saved” state
    

Once this clicks, you stop fearing complexity.

---

## How we’ll learn PostgreSQL (seriously, not skimming)

We’ll go in **layers**, each building intuition. You’ll _feel_ why Postgres exists and when it beats NoSQL.

### Phase 0 – Mindset shift (important)

PostgreSQL is:

- **Relational**
    
- **Strongly consistent**
    
- **Schema-first**
    
- **Transaction-oriented**
    
- **Query-planner driven**
    

MongoDB is:

- Schema-flexible
    
- App-enforced constraints
    
- Eventually consistent by default
    
- Write-first thinking
    

If you don’t internalize this difference, Postgres will feel “annoying”.

---

## 8️⃣ What makes PostgreSQL different from others (summary table)

| Feature            | PostgreSQL    | MySQL              | MongoDB          |
| ------------------ | ------------- | ------------------ | ---------------- |
| MVCC               | ✅ Native      | ⚠️ Partial         | ❌ Historically   |
| Transactions       | ✅ Always      | ⚠️ Engine-specific | ⚠️ Session-based |
| Schema enforcement | ✅ Strong      | ⚠️ Medium          | ❌ App-level      |
| Query planner      | ✅ Advanced    | ⚠️ Simpler         | ❌ Manual         |
| Data integrity     | ✅ Very strong | ⚠️ Medium          | ❌ Weak           |
| Flexibility        | ✅ JSONB       | ⚠️ Limited         | ✅ High           |
Postgres is built for:

> **Long-term correctness under growth**


### Why this matters

**Pros**

- Process crash ≠ entire DB crash
    
- Strong isolation between connections
    
- Easier correctness guarantees
    




### 2️⃣ Databases, schemas, tables (core mental model)

text

Copy code

`PostgreSQL server  ├── database (like a Mongo DB)  │    ├── schema (namespace)  │    │    ├── table  │    │    ├── index  │    │    └── view`

**Schema ≠ database**

By default:

sql

Copy code

`public.users public.posts`

Think of schema like:

ts

Copy code

`@Module() // namespace`

---

## Phase 2 – Data modeling (THIS is where NoSQL users struggle)

### 3️⃣ Tables are contracts, not suggestions

sql

Copy code

`CREATE TABLE users (   id UUID PRIMARY KEY,   email TEXT NOT NULL UNIQUE,   password_hash TEXT NOT NULL,   created_at TIMESTAMP DEFAULT now() );`

Key ideas:

- `NOT NULL` is enforcement, not documentation
    
- `UNIQUE` is a **guarantee**
    
- Types matter a lot
    

This replaces:

ts

Copy code

`if (!email) throw Error();`

---

### 4️⃣ Relationships (the big shift)

Mongo:

json

Copy code

`{   "userId": "...",   "posts": [...] }`

Postgres:

sql

Copy code

`CREATE TABLE posts (   id UUID PRIMARY KEY,   user_id UUID REFERENCES users(id),   title TEXT NOT NULL );`

This gives you:

- Referential integrity
    
- Cascades
    
- Guarantees no orphan records
    

---

## Phase 3 – Querying the relational way

### 5️⃣ JOINs are not expensive (when done right)

sql

Copy code

`SELECT u.email, p.title FROM users u JOIN posts p ON p.user_id = u.id;`

Postgres:

- Uses indexes
    
- Builds execution plans
    
- Chooses hash join / nested loop / merge join
    

Mongo:

- You manually optimize `$lookup`
    

---

### 6️⃣ Aggregation (Postgres destroys NoSQL here)

sql

Copy code

`SELECT user_id, COUNT(*)  FROM posts GROUP BY user_id HAVING COUNT(*) > 10;`

This runs **inside the engine**, optimized in C.

---

## Phase 4 – Transactions (Postgres superpower)

### 7️⃣ ACID transactions (you’ll love this)

sql

Copy code

`BEGIN;  INSERT INTO orders (...) VALUES (...); UPDATE users SET balance = balance - 100 WHERE id = ...;  COMMIT;`

Or rollback automatically on error.

Mongo needs:

- Sessions
    
- Replica sets
    
- Careful logic
    

Postgres: **native and default**

---

### 8️⃣ Isolation levels (important later)

- READ COMMITTED (default)
    
- REPEATABLE READ
    
- SERIALIZABLE
    

Postgres prevents:

- Dirty reads
    
- Lost updates
    
- Phantom reads (depending on level)
    

---

## Phase 5 – Indexes (performance fundamentals)

### 9️⃣ Index types (huge topic)

- B-tree (default)
    
- Hash
    
- GIN (arrays, JSONB)
    
- GiST (geo, ranges)
    
- Partial indexes
    
- Composite indexes
    

Example:

sql

Copy code

`CREATE INDEX idx_users_email ON users(email);`

Bad indexing = slow DB even with good SQL.

---


---

## Phase 7 – PostgreSQL in real apps (NestJS angle)

Since you’re a NestJS dev:

- TypeORM / Prisma / Drizzle
    
- Migrations
    
- Connection pooling
    
- Read replicas
    
- Locking issues
    
- Deadlocks
    
- N+1 queries
    

We’ll connect Postgres concepts directly to:

- Auth systems
    
- Follower systems (you already built this!)
    
- Messaging
    
- Pagination (OFFSET vs cursor)
    

---

## How I suggest we proceed (step-by-step)

If you agree, we’ll do this:

### Lesson 1 (next message)

- Install PostgreSQL
    
- psql basics
    
- Databases vs schemas
    
- First table
    
- First real inserts & queries
    

### Lesson 2

- Data modeling properly
    
- Keys, constraints, normalization
    
- Mistakes NoSQL devs make
    

### Lesson 3

- JOINs, aggregations, subqueries
    

### Lesson 4

- Transactions & isolation
    

### Lesson 5

- Indexing & performance intuition
    

---

### Before we start Lesson 1, tell me:

1. **OS** (Linux / macOS / Windows)?
    
2. Do you want:
    
    - **pure SQL first**, or
        
    - **SQL + NestJS (Prisma/TypeORM) together**?
        

Once you answer that, we start **Lesson 1 properly**, no rushing.

That single sentence explains almost everything.

---

## 1️⃣ Process-based architecture (this already makes it different)

### PostgreSQL is **process-based**, not thread-based

- Each client connection → **one OS process**
    
- The OS scheduler handles concurrency
    
- Shared memory is used for:
    
    - WAL buffers
        
    - Shared buffers (cache)
        
    - Locks
        

### Why this matters

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

---

## 2️⃣ MVCC — the core idea you must internalize

> **Postgres never overwrites data in place. Ever.**

This is the most important sentence in this lesson.

### What MVCC actually means

Instead of:

pgsql

Copy code

`UPDATE users SET name = 'A' WHERE id = 1;`

Postgres does:

sql

Copy code

`old row → kept new row → inserted`

Each row has:

- `xmin` → transaction that created it
    
- `xmax` → transaction that deleted/updated it
    

A transaction sees:

- Only rows valid **at its snapshot time**
    

### Consequences (huge)

- Readers never block writers
    
- Writers never block readers
    
- Long reads don’t stall writes
    
- Consistent reads by default
    

This is **not true** in many other systems.

---

## 3️⃣ Why “reads don’t block writes” is not magic

Let’s be concrete.

### In a locking-based system

- Read → shared lock
    
- Write → exclusive lock
    
- Conflict = waiting
    

### In PostgreSQL

- Reader sees old version
    
- Writer creates new version
    
- No conflict
    

💡 **Postgres trades disk space for concurrency**

That’s a _design philosophy_, not an accident.

---

## 4️⃣ “Every row has versions” (and why that matters)

A table is **not** just rows.

It’s:

java

Copy code

`row v1 (dead) row v2 (visible) row v3 (future)`

Postgres tracks:

- Visibility per transaction
    
- Not per query
    

This enables:

- Snapshot isolation
    
- Repeatable reads
    
- Serializable transactions
    

But also introduces:

- Table bloat
    
- Need for VACUUM
    

We’ll revisit this later.

---

## 5️⃣ WAL — Write-Ahead Logging (durability, not speed)

> **Postgres never considers data committed until it’s written to WAL.**

### What WAL is

- Sequential append-only log
    
- Written **before** data pages
    
- Used for:
    
    - Crash recovery
        
    - Replication
        
    - Point-in-time recovery
        

### Why this matters

- Sequential writes are fast
    
- Random page writes are slow
    
- WAL allows:
    
    - High durability
        
    - Predictable recovery
        

🔹 **Important insight**  
Postgres prioritizes:

> **Correctness after crash** over peak performance

This is why banks love it.

---

## 6️⃣ Cost-based query planner (Postgres _thinks_ before running)

Postgres does not execute SQL as written.

It does:

1. Parse SQL
    
2. Rewrite
    
3. Generate multiple plans
    
4. Estimate cost
    
5. Pick cheapest plan
    

### Cost ≠ time

Cost is:

- I/O operations
    
- CPU estimates
    
- Memory usage
    

Example:

sql

Copy code

`SELECT * FROM posts WHERE user_id = '...';`

Postgres decides:

- Index scan?
    
- Seq scan?
    
- Bitmap index scan?
    

You don’t command it — you **guide it**.

This is why understanding indexes matters.

---

## 7️⃣ “Real transactions” (this is not marketing)

Postgres transactions are:

- ACID by default
    
- No configuration needed
    
- No cluster requirements
    

### Atomic

All or nothing.

### Consistent

Constraints always enforced.

### Isolated

MVCC-backed isolation.

### Durable

WAL-backed durability.

MongoDB only achieved this **years later**, and with caveats.

---

## 8️⃣ What makes PostgreSQL different from others (summary table)

|Feature|PostgreSQL|MySQL|MongoDB|
|---|---|---|---|
|MVCC|✅ Native|⚠️ Partial|❌ Historically|
|Transactions|✅ Always|⚠️ Engine-specific|⚠️ Session-based|
|Schema enforcement|✅ Strong|⚠️ Medium|❌ App-level|
|Query planner|✅ Advanced|⚠️ Simpler|❌ Manual|
|Data integrity|✅ Very strong|⚠️ Medium|❌ Weak|
|Flexibility|✅ JSONB|⚠️ Limited|✅ High|

Postgres is built for:

> **Long-term correctness under growth**

---

## 9️⃣ Why this matters for leadership (important for you)

You said:

> _“I am unable to make people understand and thus me being unable to lead.”_

This is the missing layer 👇

Junior dev says:

> “Postgres is slow compared to Mongo.”

You say:

> “Postgres trades write amplification and storage for correctness, concurrency, and predictable behavior under load. Mongo optimizes for developer speed and flexible schema.”

That’s leadership.

You don’t argue tools.  
You argue **tradeoffs**.

---

## Mental model to keep forever

Think of PostgreSQL as:

> **A time-traveling ledger of facts with strict rules about reality.**

- Nothing disappears immediately
    
- Everything is versioned
    
- Truth depends on time (transaction snapshot)
    
- Rules are enforced at the engine level
    

---

## What we do next (no SELECT * yet)

Next lesson we will cover:

👉 **Data modeling philosophy**

- Why normalization exists
    
- Why constraints are power
    
- Why “schema rigidity” is actually safety
    

Only **after that** will we touch SQL queries.

If you want, next message I’ll explain:

- **Why NoSQL feels easier**
    
- **Why relational databases feel harder**
    
- And **why that pain is intentional**
    

Just say the word.

##### You said:

jesus, this is so knowledge full but big big concepts and words. So before even diving into doing SQL query(I already have done it quite often but it has been some time), what are the concept like this that I need to know before diving ? You are eloquently guiding me.

###### ChatGPT said:

You’re reacting **exactly** the right way.  
What you’re feeling is the gap between _using a tool_ and _understanding a system_. That gap is where seniority and leadership live.

Let me do something very deliberate now:

> I’ll give you a **small, finite map** of concepts you must understand **before touching SQL again**,  
> explained in **human language**, not textbook language.

No jargon dump. No skipping. No ego words.

---

# The _Pre-SQL_ Mental Model for PostgreSQL

_(What you must know before writing queries)_

There are **only 8 core concepts**.  
Everything else is a detail of these.

---



---

## 3️⃣ Concurrency is normal, not exceptional

Postgres assumes:

- Hundreds of users
    
- Thousands of queries
    
- All at the same time
    
- On the same data
    

So it is designed for **conflict**, not avoidance.

### This leads to:

- Snapshot isolation
    
- Transaction boundaries
    
- Explicit locking (when needed)
    

If you think in “one request at a time”, you will design bad schemas.

---

## 4️⃣ Data integrity is a _first-class feature_

In PostgreSQL:

- `NOT NULL` is not decoration
    
- `FOREIGN KEY` is not optional
    
- `UNIQUE` is not just an index
    

They are **rules of reality**.

### Mental shift

Instead of:

ts

Copy code

`if (!email) throw error`

Think:

> “It is impossible for an invalid row to exist.”

This reduces:

- Bugs
    
- Defensive code
    
- Mental load
    

Great leaders _remove_ problems instead of handling them repeatedly.

---

## 5️⃣ Storage is not the same as memory

This sounds obvious — but people forget it.

Postgres constantly balances:

- Disk I/O
    
- Memory cache
    
- WAL writes
    
- Background cleanup
    

### Why this matters

- Indexes are not free
    
- Updates are expensive
    
- Deletes don’t delete immediately
    
- VACUUM exists for a reason
    

This explains _performance behavior_ later.

---

## 6️⃣ The database plans queries, not you

You don’t “tell” Postgres how to run a query.

You **describe what you want**.

Postgres decides:

- Join order
    
- Scan type
    
- Index usage
    

### Your job is to:

- Model data correctly
    
- Create correct indexes
    
- Write clear queries
    

Trying to micro-control execution is a junior mistake.

---

## 7️⃣ Transactions are the unit of truth

In PostgreSQL:

- A single query is already a transaction
    
- A transaction defines:
    
    - Visibility
        
    - Atomicity
        
    - Isolation
        

### This means

- Partial failure is impossible
    
- Either reality changes, or it doesn’t
    
- There is no “half-saved” state
    

Once this clicks, you stop fearing complexity.

---

## 8️⃣ PostgreSQL is conservative by design

This is the _philosophical_ point.

Postgres chooses:

- Correctness over convenience
    
- Predictability over magic
    
- Explicitness over guesswork
    

That’s why:

- Migrations feel heavy
    
- Schemas feel rigid
    
- Errors feel strict
    

This is not a flaw.  
It’s the reason Postgres scales _organizationally_, not just technically.

## How we’ll learn PostgreSQL (seriously, not skimming)

We’ll go in **layers**, each building intuition. You’ll _feel_ why Postgres exists and when it beats NoSQL.

### Phase 0 – Mindset shift (important)

PostgreSQL is:

- **Relational**
    
- **Strongly consistent**
    
- **Schema-first**
    
- **Transaction-oriented**
    
- **Query-planner driven**
    

MongoDB is:

- Schema-flexible
    
- App-enforced constraints
    
- Eventually consistent by default
    
- Write-first thinking
    

If you don’t internalize this difference, Postgres will feel “annoying”.

---

## Phase 1 – PostgreSQL as a system (not SQL yet)

### 1️⃣ What PostgreSQL actually is

Postgres is:

- A **process-based database**
    
- With:
    
    - MVCC (Multi-Version Concurrency Control)
        
    - WAL (Write-Ahead Log)
        
    - Cost-based query planner
        
    - Real transactions
        

This means:

- Reads don’t block writes
    
- Writes don’t block reads
    
- Every row has versions
    

We’ll revisit this often.

---

### 2️⃣ Databases, schemas, tables (core mental model)

text

Copy code

`PostgreSQL server  ├── database (like a Mongo DB)  │    ├── schema (namespace)  │    │    ├── table  │    │    ├── index  │    │    └── view`

**Schema ≠ database**

By default:

sql

Copy code

`public.users public.posts`

Think of schema like:

ts

Copy code

`@Module() // namespace`

---

## Phase 2 – Data modeling (THIS is where NoSQL users struggle)

### 3️⃣ Tables are contracts, not suggestions

sql

Copy code

`CREATE TABLE users (   id UUID PRIMARY KEY,   email TEXT NOT NULL UNIQUE,   password_hash TEXT NOT NULL,   created_at TIMESTAMP DEFAULT now() );`

Key ideas:

- `NOT NULL` is enforcement, not documentation
    
- `UNIQUE` is a **guarantee**
    
- Types matter a lot
    

This replaces:

ts

Copy code

`if (!email) throw Error();`

---

### 4️⃣ Relationships (the big shift)

Mongo:

json

Copy code

`{   "userId": "...",   "posts": [...] }`

Postgres:

sql

Copy code

`CREATE TABLE posts (   id UUID PRIMARY KEY,   user_id UUID REFERENCES users(id),   title TEXT NOT NULL );`

This gives you:

- Referential integrity
    
- Cascades
    
- Guarantees no orphan records
    

---

## Phase 3 – Querying the relational way

### 5️⃣ JOINs are not expensive (when done right)

sql

Copy code

`SELECT u.email, p.title FROM users u JOIN posts p ON p.user_id = u.id;`

Postgres:

- Uses indexes
    
- Builds execution plans
    
- Chooses hash join / nested loop / merge join
    

Mongo:

- You manually optimize `$lookup`
    

---

### 6️⃣ Aggregation (Postgres destroys NoSQL here)

sql

Copy code

`SELECT user_id, COUNT(*)  FROM posts GROUP BY user_id HAVING COUNT(*) > 10;`

This runs **inside the engine**, optimized in C.

---

## Phase 4 – Transactions (Postgres superpower)

### 7️⃣ ACID transactions (you’ll love this)

sql

Copy code

`BEGIN;  INSERT INTO orders (...) VALUES (...); UPDATE users SET balance = balance - 100 WHERE id = ...;  COMMIT;`

Or rollback automatically on error.

Mongo needs:

- Sessions
    
- Replica sets
    
- Careful logic
    

Postgres: **native and default**

---

### 8️⃣ Isolation levels (important later)

- READ COMMITTED (default)
    
- REPEATABLE READ
    
- SERIALIZABLE
    

Postgres prevents:

- Dirty reads
    
- Lost updates
    
- Phantom reads (depending on level)
    

---

## Phase 5 – Indexes (performance fundamentals)

### 9️⃣ Index types (huge topic)

- B-tree (default)
    
- Hash
    
- GIN (arrays, JSONB)
    
- GiST (geo, ranges)
    
- Partial indexes
    
- Composite indexes
    

Example:

sql

Copy code

`CREATE INDEX idx_users_email ON users(email);`

Bad indexing = slow DB even with good SQL.

---

## Phase 6 – PostgreSQL vs NoSQL (hybrid thinking)

Postgres supports:

sql

Copy code

`JSONB ARRAY ENUM RANGE UUID`

You can do:

sql

Copy code

`metadata JSONB`

Meaning:  
👉 You don’t lose flexibility  
👉 You gain constraints + transactions

---

## Phase 7 – PostgreSQL in real apps (NestJS angle)

Since you’re a NestJS dev:

- TypeORM / Prisma / Drizzle
    
- Migrations
    
- Connection pooling
    
- Read replicas
    
- Locking issues
    
- Deadlocks
    
- N+1 queries
    

We’ll connect Postgres concepts directly to:

- Auth systems
    
- Follower systems (you already built this!)
    
- Messaging
    
- Pagination (OFFSET vs cursor)
    

---

## How I suggest we proceed (step-by-step)

If you agree, we’ll do this:

### Lesson 1 (next message)

- Install PostgreSQL
    
- psql basics
    
- Databases vs schemas
    
- First table
    
- First real inserts & queries
    

### Lesson 2

- Data modeling properly
    
- Keys, constraints, normalization
    
- Mistakes NoSQL devs make
    

### Lesson 3

- JOINs, aggregations, subqueries
    

### Lesson 4

- Transactions & isolation
    

### Lesson 5

- Indexing & performance intuition
    

---

### Before we start Lesson 1, tell me:

1. **OS** (Linux / macOS / Windows)?
    
2. Do you want:
    
    - **pure SQL first**, or
        
    - **SQL + NestJS (Prisma/TypeORM) together**?
        

Once you answer that, we start **Lesson 1 properly**, no rushing.

## 8️⃣ What makes PostgreSQL different from others (summary table)

|Feature|PostgreSQL|MySQL|MongoDB|
|---|---|---|---|
|MVCC|✅ Native|⚠️ Partial|❌ Historically|
|Transactions|✅ Always|⚠️ Engine-specific|⚠️ Session-based|
|Schema enforcement|✅ Strong|⚠️ Medium|❌ App-level|
|Query planner|✅ Advanced|⚠️ Simpler|❌ Manual|
|Data integrity|✅ Very strong|⚠️ Medium|❌ Weak|
|Flexibility|✅ JSONB|⚠️ Limited|✅ High|

Postgres is built for:

> **Long-term correctness under growth**

---
---

## 2️⃣ Time exists inside the database (MVCC intuition)

Postgres is **time-aware**.

Not in a sci-fi way — in a _consistency_ way.

### Simple idea

Different users can see **different versions of the same row** at the same time — and _both are correct_.

That’s it. That’s MVCC.

### Why this matters

- Reads don’t block writes
    
- “What you see” depends on _when_ you started your transaction
    
- The database is not a single global truth at every instant
    

This is extremely unintuitive at first — but incredibly powerful.



### 1️⃣ What PostgreSQL actually is

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
    

We’ll revisit this often.

---

### 2️⃣ Databases, schemas, tables (core mental model)

`PostgreSQL server  ├── database (like a Mongo DB)  │    ├── schema (namespace)  │    │    ├── table  │    │    ├── index  │    │    └── view`

**Schema ≠ database**

By default:

`public.users public.posts`

Think of schema like:

`@Module() // namespace`

---

## Phase 2 – Data modeling (THIS is where NoSQL users struggle)

### 3️⃣ Tables are contracts, not suggestions

`CREATE TABLE users (   id UUID PRIMARY KEY,   email TEXT NOT NULL UNIQUE,   password_hash TEXT NOT NULL,   created_at TIMESTAMP DEFAULT now() );`

Key ideas:

- `NOT NULL` is enforcement, not documentation
    
- `UNIQUE` is a **guarantee**
    
- Types matter a lot
    

This replaces:

`if (!email) throw Error();`

---

### 4️⃣ Relationships (the big shift)

Mongo:

`{   "userId": "...",   "posts": [...] }`

Postgres:

`CREATE TABLE posts (   id UUID PRIMARY KEY,   user_id UUID REFERENCES users(id),   title TEXT NOT NULL );`

This gives you:

- Referential integrity
    
- Cascades
    
- Guarantees no orphan records
    

---

## Phase 3 – Querying the relational way

### 5️⃣ JOINs are not expensive (when done right)

`SELECT u.email, p.title FROM users u JOIN posts p ON p.user_id = u.id;`

Postgres:

- Uses indexes
    
- Builds execution plans
    
- Chooses hash join / nested loop / merge join
    

Mongo:

- You manually optimize `$lookup`
    

---

### 6️⃣ Aggregation (Postgres destroys NoSQL here)

`SELECT user_id, COUNT(*)  FROM posts GROUP BY user_id HAVING COUNT(*) > 10;`

This runs **inside the engine**, optimized in C.

---

## Phase 4 – Transactions (Postgres superpower)

### 7️⃣ ACID transactions (you’ll love this)

`BEGIN;  INSERT INTO orders (...) VALUES (...); UPDATE users SET balance = balance - 100 WHERE id = ...;  COMMIT;`

Or rollback automatically on error.

Mongo needs:

- Sessions
    
- Replica sets
    
- Careful logic
    

Postgres: **native and default**

---

### 8️⃣ Isolation levels (important later)

- READ COMMITTED (default)
    
- REPEATABLE READ
    
- SERIALIZABLE
    

Postgres prevents:

- Dirty reads
    
- Lost updates
    
- Phantom reads (depending on level)
    

---

## Phase 5 – Indexes (performance fundamentals)

### 9️⃣ Index types (huge topic)

- B-tree (default)
    
- Hash
    
- GIN (arrays, JSONB)
    
- GiST (geo, ranges)
    
- Partial indexes
    
- Composite indexes
    

Example:

`CREATE INDEX idx_users_email ON users(email);`

Bad indexing = slow DB even with good SQL.

---

## Phase 6 – PostgreSQL vs NoSQL (hybrid thinking)

Postgres supports:

`JSONB ARRAY ENUM RANGE UUID`

You can do:

`metadata JSONB`

Meaning:  
👉 You don’t lose flexibility  
👉 You gain constraints + transactions

---

## Phase 7 – PostgreSQL in real apps (NestJS angle)

Since you’re a NestJS dev:

- TypeORM / Prisma / Drizzle
    
- Migrations
    
- Connection pooling
    
- Read replicas
    
- Locking issues
    
- Deadlocks
    
- N+1 queries
    

We’ll connect Postgres concepts directly to:

- Auth systems
    
- Follower systems (you already built this!)
    
- Messaging
    
- Pagination (OFFSET vs cursor)
    

---

## How I suggest we proceed (step-by-step)

If you agree, we’ll do this:

### Lesson 1 (next message)

- Install PostgreSQL
    
- psql basics
    
- Databases vs schemas
    
- First table
    
- First real inserts & queries
    

### Lesson 2

- Data modeling properly
    
- Keys, constraints, normalization
    
- Mistakes NoSQL devs make
    

### Lesson 3

- JOINs, aggregations, subqueries
    

### Lesson 4

- Transactions & isolation
    

### Lesson 5

- Indexing & performance intuition
    

---

### Before we start Lesson 1, tell me:

1. **OS** (Linux / macOS / Windows)?
    
2. Do you want:
    
    - **pure SQL first**, or
        
    - **SQL + NestJS (Prisma/TypeORM) together**?
        

Once you answer that, we start **Lesson 1 properly**, no rushing.
### Basic SQL
Those are things like Data Retrieval, Filtering, Basic Aggregation and Grouping Data. 
###### Data Retrieval
###### Filtering
###### Basic Aggregation
###### Grouping Data

### Joins
### Normalization
### Window Function
### Indexing & Performance
### Sub queries

### Basic SQL
Things like ==Data Retrieval==, ==Filtering== , ==Basic Aggregation== and ==Grouping data==. An Aggregate function is a function that performs a calculation on a set of values, and returns a single value. Some of the aggregate functions are `MAX()`, `MIN()`, `COUNT()`, `SUM()`, `AVG()` and other depending on database. It ignores NULL values except `Count()`. It is evaluated after `WHERE` clause. When we use aggregate function, we just get one result and that column name will be not descriptive and random. To solve that we use Alias. It is used as `AS`. We can put multiple column in one column and give it different name through aliasing. Like we have different fields for postal code, city, state and we need all column in same. we can achieve through aliasing.

We can also use limit but it is different for different database. Like Limit is used my MySQL, FETCH FIRST by ORACLE and so on. 

There is UNION(it return all unique value) and UNION ALL(return all value) also. It works as to combine two or more than two result of `SELECT`.
```sql
SELECT City, Country FROM Customers  
WHERE Country='Germany'  
UNION ALL -- It gives all the result while Just UNION gives unique only results. 
SELECT City, Country FROM Suppliers  
WHERE Country='Germany'  
ORDER BY City;
```

For grouping data, GROUP BY is used. Like lets say we have customer from different city, some may live in same city. What if we want to number of customers that live in one city ? Or we want to see how many people work at one branch ? `GROUP BY` is used. However, GROUP BY doesn't work for all. Like we need something in select for it to work. We can't use * in `GROUP BY`. Now, after the rows has been calculated using `GROUP BY`, we need to use `HAVING` as `WHERE` doesn't work after the operation is done. Remember, it is not that `WHERE` is not usable but it has already been applied and and now we need to do filtering after all previous operation ? Like we need to display only cities which have more than certain customer ? Then `HAVING` is used. `WHERE` can't be used as it has already been applied before `GROUP BY`.  

For Operators There are Lot of types included. Like there is Arithmetic Operators like addition, sub, mul, div, and modulo also. Comparison Operators, just like name greater than, less than, not equal to and other things. Also Bitwise Operators like &, | and ^ (it is exclusive OR). And the most important operators like AND, OR, EXIST and other things.

There are other things like `ANY`, `OR`, `ALL` which is better to learn at the time of use rather than listing here.
``` sql
-- Retrieve data with filtering, sorting, and pagination
SELECT DISTINCT column
-- with distinct we get distinct value. other wise lets say we have 500 people from 200 countries. So there will be 500 rows for each people. if no distinct is given, then no of row in result will be same as table but if we use distinct, there will be only 200 rows, it works for whatever column Name is typed.
FROM table
WHERE age BETWEEN 20 AND 30
  AND city NOT IN ('Los Angeles', 'Chicago') -- NOT can be added anywhere, It is seperate with IN and not same NOT and IN are different
  AND name ILIKE 'J%' -- ILIKE is case-insensitive, while LIKE is case-sensitive
  AND created_at >= '2023-01-01'
  AND name IS NULL -- IS NOT NULL
ORDER BY created_at DESC, age ASC -- first created_at and if same then only age
ORDER BY name, name DESC  -- we can sort using name and also descending order also
ORDER BY name, address  -- first name, if same name only addres
LIMIT 5 OFFSET 5

-- Group, aggregate, and filter aggregated results
SELECT city, COUNT(*) AS user_count, AVG(age) AS avg_age, MAX(age) AS oldest_age
FROM users
WHERE created_at >= '2023-01-01'
GROUP BY city
HAVING COUNT(*) > 10 AND AVG(age) < 35 -- HAVING filters groups, not rows
ORDER BY user_count DESC -- Sort groups by user count in descending order
-- HAVING is necessary for filtering aggregated values; WHERE cannot be used here.
-- Common pitfall: forgetting GROUP BY when using aggregate functions like COUNT or AVG.
```
### Joins
Used to combine rows from two or more tables. There are many types of Joins. However mainly there are two types. They are Inner join and Outer Joins. Let's say there are two tables. In Inner Joins. It returns row where there is a match on both tables. It filter out not unmatched rows. For Outer Joins, There are Left, Right and Full Outer joins. In Left Join, it return all the column for left table and matching records from right table. Same but Vice versa for Right Join. And there are Cross Join and Self Join also.
![[Pasted image 20241216153417.png]]
3. **Cross Join**
    
    - Cartesian product of two tables.
4. **Self Join**
    
    - Joining a table with itself.

### Data Manipulation
Some of the operation are Insert, Update and Delete.
```sql
INSERT INTO users (name, email, city, age, created_at) 
VALUES 
('John Doe', 'john@example.com', 'New York', 25, CURRENT_TIMESTAMP),
('Jane doe', 'jane@example.com', 'Pokhaa', 25, CURRENT_TIMESTAMP);
-- we don't need to specify (name, email ...) if all the column in single row is to be filled. and we can inset multiple row at same time

UPDATE users
SET city = 'San Francisco', age = 26 -- Update specific columns
WHERE email = 'john@example.com'; -- Always use WHERE to avoid updating all rows
-- Common pitfall: accidentally updating the entire table without a WHERE clause

DELETE FROM users
WHERE age < 18;
-- This means that the table structure, attributes, and indexes will be intact which is different from TRUNCATE
-- Common pitfall: omitting WHERE, which deletes all rows

DROP TABLE tablename; -- delete table

-- Remove all rows but keep the table structure
TRUNCATE TABLE users; 
-- Faster than DELETE for large datasets but cannot be rolled back if not in a transaction
-- TRUNCATE resets identity columns in most databases (e.g., AUTO_INCREMENT in MySQL)
```
### Schema and Table Management
We can `CREATE` database. But make sure to create with Admin privilege so that we can do anything. Same with `DROP` it. So do it carefully. We can backup to disk also. There is a option of differential also. Meaning it will only back up data that have been changed from last. There is create table features also.
While creating table we can include constraints like 
`NOT NULL`, 
`UNIQUE`, 
`PRIMARY KEY`, it is combination of `NOT NULL` and `UNIQUE` . There is only one PK in one table. The thing is that we can use two column as primary key. Like there is id and roll no, we can use combination of two value as primary key.
`FOREIGHN KEY`, it refers to primary key in another table. The table with Primary key is called parent table and table with FK is called child table.
`CHECK`, `DEFAULT`,
`CREATE INDEX`
There is also feature to `DROP` the table. Also to `TRUNCATE` the table. `Drop` will delete the table while `TRUNCATE` will only delete the data and not table. We can also use `ALTER` the database like adding column, delete column, modify and it is also used to change the data type of required columns.

Indexing is also available and it speeds up searching however it requires more time to update. So only put index on column that will be frequently searched against or will be searched.

For Data Types look for them in internet as every database have different data types. Some are same like Int, Date, Varchar and many more.

==Do Read about Date format before doing anything== . As it is extremely hard to memorize and I will not write here. And don't use Time component unless you have to. It is not worth it as it introduce next level of bugs.
```sql
CREATE TABLE ComprehensiveOrders (
    OrderID INT NOT NULL AUTO_INCREMENT, -- Auto-incremented unique identifier
    CustomerID INT NOT NULL, -- Foreign key to Customers table
    ProductID INT DEFAULT NULL, -- Optional product reference
    OrderDate DATETIME DEFAULT CURRENT_TIMESTAMP, -- Auto-sets to the current timestamp
    DeliveryDate DATE, -- Expected delivery date
    OrderAmount DECIMAL(12, 2) CHECK (OrderAmount >= 0), -- Total with validation for non-negative values
    PaymentStatus VARCHAR(20) DEFAULT 'Pending', -- Status with default
    IsPaid BOOLEAN DEFAULT FALSE, -- Payment status, true/false
    PaymentMethod ENUM('Cash', 'Card', 'Online', 'Other') DEFAULT 'Cash', -- Restricts input to predefined methods
    Notes TEXT, -- Additional order details
    DiscountCode CHAR(10), -- Fixed-length discount code
    OrderPriority TINYINT UNSIGNED DEFAULT 0, -- Priority levels (0 = Normal, 1 = High)
    TotalWeight FLOAT DEFAULT 0.0, -- Approximate total weight of items
    TaxRate DOUBLE(5, 3) CHECK (TaxRate BETWEEN 0 AND 1), -- Percentage range validation for tax
    ShipmentID BINARY(16), -- Unique binary identifier, e.g., UUID
    ProductData JSON, -- Flexible structure for product metadata
    OrderStatus SET('Pending', 'Processing', 'Shipped', 'Delivered', 'Cancelled'), -- Multiple selectable states
    PRIMARY KEY (OrderID, CustomerID), -- Composite primary key using two columns
    UNIQUE (DiscountCode), -- Ensures unique discount codes
    INDEX idx_OrderDate (OrderDate), -- Improves search performance on OrderDate
    FULLTEXT INDEX ft_Notes (Notes), -- Enables full-text search for Notes
    FOREIGN KEY (CustomerID) REFERENCES Customers(CustomerID) 
        ON DELETE CASCADE 
        ON UPDATE CASCADE, -- Deletes or updates linked customer rows automatically
    FOREIGN KEY (ProductID) REFERENCES Products(ProductID) 
        ON DELETE SET NULL, -- Nullifies ProductID if related product is deleted
    CONSTRAINT chk_OrderAmount CHECK (OrderAmount >= 0), -- Named constraint for validation
    CONSTRAINT fk_ShipmentID FOREIGN KEY (ShipmentID) REFERENCES Shipments(ShipmentID) -- Explicit foreign key
) ENGINE=InnoDB -- InnoDB supports transactions and foreign keys
AUTO_INCREMENT=1001 -- Start ID generation from 1001
DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci -- Multilingual support with case-insensitive collation
COMMENT = 'Comprehensive table showcasing SQL table options';
```
### **Indexes**

1. **Purpose of Indexes**
    
    - Improve query performance.
2. **Types of Indexes**
    
    - Single-column index.
    - Composite index (multiple columns).
3. **Creating and Dropping Indexes**
    
    - `CREATE INDEX`, `DROP INDEX`.

---

### Advanced SQL
We can copy data from one table to another using `SELECT INTO`. That new table will be created. However if new table is already present, then we can use `INSERT INTO SELECT`. Data source type and target should match. Let's say we want to get if else in query like below one then `CASE` is used.
```sql
SELECT CustomerName, City, Country  
FROM Customers  
ORDER BY  
(CASE  
    WHEN City IS NULL THEN Country  
    ELSE City  
END);
```
Then, after that, we can also use SQL function like `IFNULL`, `COALENSE` to check for the value is NULL or not and if it is NULL, then return something else. Like in below thing. No need to rote it as different database has different syntax to achieve it.
```sql
	SELECT ProductName, UnitPrice * (UnitsInStock + IFNULL(UnitsOnOrder, 0))  
	FROM Products;
```
And there is also function like thing in SQL called stored procedures. and we can send parameters too 😍😍😍.

1. **Subqueries**
    - Single-row subqueries.
    - Multi-row subqueries (`IN`, `ANY`, `ALL`).
    - Correlated subqueries.
2. **Common Table Expressions (CTEs)**
    
    - `WITH` clause for temporary result sets.
3. **Window Functions**
    
    - `ROW_NUMBER`, `RANK`, `DENSE_RANK`, `NTILE`.
    - Aggregate functions with `OVER` clause (e.g., `SUM`, `AVG`).
4. **Set Operations**
    
    - `UNION`, `UNION ALL`, `INTERSECT`, `EXCEPT`.
5. **Recursive Queries**
    
    - Recursive `WITH` queries.

---

### **Performance Optimization**

1. **Execution Plans**
    
    - Understand how queries are executed.
2. **Query Optimization**
    
    - Index usage, reducing nested subqueries, avoiding `SELECT *`.
3. **Normalization and Denormalization**
    
    - Understand normalization forms (1NF, 2NF, 3NF).
4. **Partitioning**
    
    - Horizontal and vertical partitioning.

---

### **Database Administration**

1. **Transactions**
    
    - `BEGIN`, `COMMIT`, `ROLLBACK`.
2. **ACID Properties**
    
    - Atomicity, Consistency, Isolation, Durability.
3. **Views**
    
    - Create reusable query logic.
4. **Stored Procedures**
    
    - Encapsulate business logic in the database.
5. **Triggers**
    
    - Automate actions based on table events.

---

### **Practical Skills**

1. **Designing Database Schemas**
    
    - ER diagrams, relationships, normalization.
2. **Backup and Restore**
    
    - Understand `BACKUP` and `RESTORE` mechanisms.
3. **Database Security**
    
    - User roles, permissions, and `GRANT/REVOKE`.
4. **Using SQL with Programming Languages**
    
    - Interact with databases using Node.js (`pg` or `mysql2`), Python (`sqlalchemy`), etc.