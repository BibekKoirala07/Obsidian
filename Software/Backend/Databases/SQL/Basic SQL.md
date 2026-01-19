Once you answer that, we start **Lesson 1 properly**, no rushing.

###### 8️⃣ What makes PostgreSQL different from others (summary table)

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

###### Data Types
For Numerals, We have plenty of types like 
```sql
smallint(2 byte)
integer/int(4 byte)
bigint(8 byte) 
decimal/numeric() 
real(4 byte floating) 
double precision(8 byte floating point) 
serial(auto incrementing integer of 4 bytes) 
bigserial(auto incrementing interger of 8 bytes)
```
We must use numeric for cases like money or counts

For String, We have 
```sql
char(n) fixed lenght string
varchar(n) variable-length string up to n
text variable-lenght string, unlimited
citext case-insensitive text, used for emails or usernames
```

For Boolean, We have
```sql
boolean(TRUE, FALSE, NULL)
```

For UUID, We have
```sql
uuid  Universally unique identifier
```

For Arrays
```sql
integer[] or text[]
```

###### WHERE
Always prefer `WHERE` instead of `HAVING` if possible
###### Having
Having don't use indexes as Having runs after aggregation. 
###### Joins
==SQL Joins are graph traversal and not table merging==. 

Inner joins ❌ Joining without thinking about cardinality. This is common mistakes

 🔹 Data retrieval patterns
>>>>>>> f2376b6a631f041adb50707bf5aa9a1b0c996f69

- `WHERE` vs `HAVING` (filter before vs after aggregation)
    
- `IN` vs `EXISTS` (performance & semantics)
    
- `JOIN` vs subquery (readability vs planner optimization)
    
- `LEFT JOIN` + `WHERE` pitfalls (turning it into INNER JOIN)
    
- Anti-joins (`NOT EXISTS`)
    

🔹 Aggregation & grouping (why it matters)

- Grouping cardinality explosion
    
- Aggregates on indexed columns
    
- `COUNT(*)` vs `COUNT(column)`
    
- Partial aggregates vs full scans
    

 Ordering & limiting

- `ORDER BY` cost
    
- Index-backed ordering
    
- `LIMIT + OFFSET` problems at scale
    
- Cursor-based pagination (keyset pagination)

==If you need to aggregate twice -> you need a subquery or window function==
###### Self Join, Group By And Window Function
1. **Aggregation**: You can create sums, averages, counts, max, min, etc. across related sets of data. For example, calculating a running total of sales.
    
2. **Ranking**: You can rank items in your data set. For example, listing top salespeople, top 10 students, etc.
    
3. **Value comparison**: You can compare values in a data set. For example, compare the current value with the previous value to find trends or patterns.
    
4. **Rolling Aggregates**: Comparing specific ranks of values and aggregating if needed. You will learn the `ROWS BETWEEN` clause here.

###### Sub queries
There are two kinds of sub queries. They are co-related and non-correlated sub queries. 
###### Stored Procedures
```sql
CREATE OR REPLACE PROCEDURE proc_name(
	IN p_first_name TEXT,
	IN p_age NUMERIC DEFAULT 3000,
	OUT p_id INT,
)
LANGUAGE plpgsql (procedural sql(It has IF, LOOP variables))
SECURITY DEFINER (gets creater priviledge)
SET search_path = public
AS $$
BEGIN
	
	COMMIT;
END;
$$;
```
`Security Definer` means the procedure runs with the privilege of the user who created it and not who called it, the opposite is default behavior.

Stored procedure is executable code stored inside the database(Stored logic). They must be treated as workflows rather than functions. ==Stored procedure can be really helpful in terms of security. You can grant permission to run a procedure without granting access to table. ==.

==Caution==
- There should be no business logic inside stored procedures. (It should belong to application code(node))
- They are invisible to most dev tolling, and require DB expertise and create a "DB god" bottleneck. 
- Testing is extremely difficult for SP, so is versioning, and portability is low and debugging is weak and only performance is better sometimes. 
That is the reason why Modern backend systems use fewer stored procedures. ==They are normally used for DB workflows/batch jobs==. In PostgreSQL, Stored procedure run inside backend processes, share memory and locks and can impact overall DB performance. What will happen if there is bad loop in a procedure. Since it is not isolated like app code, it blocks resources, affects all queries also. 

###### Window Function

###### CTE
```sql
WITH cte_name AS (
	SELECT **
	FROM users
)
table_without_password_and_active AS (
	SELECT id, username, email
	FROM cte_name
	WHERE active = true
)
SELECT * FROM table_without_password
```
CTE's are temporary and scope limited. They don't store data. It just stores the query, not the result. It is just read only. We can't write using CTE. It is just scope to the query that defined it. ==They make our query readable and modular. ==

We also have Recursive CTE also. And it is the place where CTE's become powerful. 

Why use it ? 
Re usability, Readability, Modularity and Recursive Queries

Caution
###### Triggers
==Use triggers when when we must and keep them stupid==
==Triggers are for enforcing invariant, not implementing behavior==. 
```sql
CREATE TRIGGER set_updated_at
BEFORE UPDATE ON users
FOR EACH ROW
EXECUTE FUNCTION set_timestamp()
```
Trigger run outside application awareness. 

==Caution==
- No Business logic in triggers  pls
- Cross tables mutation. This hide cascades, deadlocks and performance nightmares. 
- In fact we can do http calls, messaging and file writes in trigger, but we must never reach outside the database. 
- No performance heavy logic in triggers, 
- Triggers are dangerous as they are invisible execution. It creates Debugging hell
- ORM hate triggers, cuz they expects that writes do exactly what they say, row counts match expectations. Triggers violate this basic thing. 

If you want next, we can:

- Walk through a **real “bad trigger” postmortem**
    
- Compare **triggers vs application-level constraints**
    
- Decide **when constraints beat triggers**
    
- Or design a **safe trigger checklist**

###### Data Retrieval
```sql
Client
  ↓
Authentication (pg_hba.conf)
  ↓
Role Login (LOGIN privilege)
  ↓
Authorization (GRANT / REVOKE)
  ↓
Object-level permissions (DB / schema / table / column)

```

###### Views and Materialization
==We don't index views but base table. We do index Materialized views==. 

Views is just like a stored query that the database expands inline when used. 

Like If we have this View:
```sql
CREATE VIEW safe_users AS
SELECT id, username 
FROM users
```
Then we can use it as a function
Like
```sql
SELECT * FROM safe_users WHERE id = 10;
```
And this above SQL query behaves as you wrote: 
```sql
SELECT id, username FROM users WHERE id = 10;
```
You see it acts as a function without parameters. We can hide sensitive fields like `password` in views and reuse it later. Also we can write tedious JOIN query in Views and reuse it later when it is needed. Human makes mistakes, new devs join, ORM sometimes do `SELECT *`. So try to create Views. 

Views don't have any caching facility at all. It may look like but no. The query you wrote in views is directly expanded or appended in query where that view in involved. That is it. Nothing complex. However real value is in control, safety and consistency. Views don't store results nor they bypass index or planner and nor they make queries faster. It is the duty of query planner that inline the view.

And guess what as long as views remain stable, the app(api) don't break right ? We can change the table structure, split table and rename column also. This matter a lot in large system, data teams and backward compatibility. 

==The main goal of Views in to help control exposure==.

By the name it sounds like Views are just made for `SELECT` query right ? But You can write using View also. However there is a catch. Views is updatable only if
==Each row of view maps to exactly one row in ONE base table==.
This is the golden rule. However don't do write with Views. It is architecturally fragile and Operationally unsafe. 

Lets talk about performance for Views.
Never hide business login in Views. Views aren't business-rules engines. 

ORMs teat view as Read-only tables. They don't assume that views is used for Inserting , updating, and deleting work. Even if the DB technically allows it. 


Now let's move to Materialized Views
MV is about performance architecture.  
Let say we create MV like this
```sql
CREATE MATERIALIZED VIEWS daily_sales AS
SELECT 
	date(created_at) AS day,
	SUM(total) AS revenue
FROM orders
GROUP BY day;
```
After creating this MV, the query is executed once. the result is stored exactly like a regular table. Thus making read operation fast. MV stores data. 

==Material Views need to be indexed manually== It need to happen immediately after creation of MV.

It exists to solve the problem of running expensive queries (like JOIN, GROUP BY, aggregates, etc. )every time. Even if the field has index, expensive queries has to run every time. So to solve this problem, MV is written once, executed once and saved in disk as table. When the request comes for MV, It is read from that table.

Materialized Views has a life cycle. 
- Creation
- Reading
- Refreshing `REFRESH MATERIALIZED daily_sales;`. This rerun the query and replaces stored data. It is about being able to read data despite at the time of updating. Nothing special. 

==When using MV, We are explicitly choosing Staleness.==
So we must update the data in MV. We can do it using 
- Manual Refresh
- Or by using Scheduled Refresh(like cron job, Background worker, Nightly Batch).
- Or Concurrent refresh(only available in PostgreSQL)







 2️⃣ How are they stored (physically)?

Postgres stores materialized views as:

> **Heap tables** (row-oriented storage)

Same as:

- Regular tables
    
- Temp tables
    

Each row:

- Stored in 8KB pages
    
- Managed by MVCC
    
- Has tuple headers (xmin/xmax)
    

📌 **Materialized views participate in MVCC just like tables.**

This is important.

If you want next:

- Why materialized views + replicas can hurt
    
- Incremental refresh strategies (and why Postgres lacks native support)
    
- When to DROP + CREATE vs REFRESH
    
- Materialized views vs partitioned tables


 8️⃣ Final mental model (important)

> **A materialized view is literally a table managed by the database, not by you.**

The only special thing about it:

- How it’s populated
    
- How it’s refreshed
    

Everything else:

- Storage
    
- Indexing
    
- Locks
    
- MVCC
    

👉 **Exactly like tables**.

- Compare **materialized views vs incremental aggregation**
    
- Talk about **why triggers + materialized views are dangerous**


###### Keys
**Primary Key**
PK should be highly stable. Example, Phone number can't be used as PK for people as it can change easily. Primary Key can't be NULL at all. PK can be one column or more depending on the architecture of table. We can use `SERIAL`, `UUID4`, `UUID7`, `BIGSERIAL` `IDENTITY` also here. 

**Candidate Key**
It just means that whichever columns can be candidate for PK. We know PK are highly stable and unique, right ? But what if we have more than one column who fulfill that role ? So those keys are Candidate Keys. And let's say we have table with one column being State Capital or District Capital. Can it be CK ? Well it seems like ? But what if some state change the capital ? Can we guarantee that Capital will be same ? Well no(may be both could have State Capital as Maryland right ? ), It could depends on time(may be in 1880, There was Augusta but for some other state right now, the capital is also Augusta) right ? So CK must exhibit uniqueness over time rather than across just one instance. Just like PK. 

Super Key

Alternate Key
Other candidate Key except PK are Alternate Keys

Foreign Key

 Surrogate Key 
 They are the artificial system created keys like Keys with `uuid4`. We know that PK don't have any business meaning right ? It is just used to fetch. and other also 
 
 Natural Key 
 Opposite of Surrogate Key. they have business meaning
 
 Simple Key
 
Super Key
Any set of columns that uniquely identifies a row. So Candidate key are minimal Super Key
 
 Composite Key
 Key made from multiple columns
 
 Compound Key
 
 Intelligent Key


###### Row Level Security
###### EXPLAIN AND EXPLAIN ANALYZE
Use cases

- Geospatial (PostGIS)
    
- Range types
    
- Similarity search
    

```sql
CREATE INDEX ON places USING GiST(location);
```

**Interview**

> “GiST is extensible and supports custom distance operators.”

---
###### 4.5 BRIN (Block Range Index)

##### Designed for

- **Huge tables**
    
- Data physically ordered (time-series)
    

```sql
CREATE INDEX ON logs USING BRIN(created_at);
```

**Why BRIN?**

- Very small index
    
- Fast for append-only tables
    

**Tradeoff**

- Less precise
    
- Might scan extra blocks
    

**Interview**

> “BRIN works best when column values correlate with physical order.”


###### 6️⃣ Covering Index (INCLUDE)

Postgres can return data **without hitting heap**

```sql
CREATE INDEX idx_orders_user
ON orders(user_id)
INCLUDE (total_amount, status);
```

Used for **index-only scans**

Requirements:

- Visibility map must allow it
    
- Table must be vacuumed
    

**Interview gold**

> “INCLUDE allows covering queries without affecting index ordering.”

---

##### 7️⃣ Partial Index (Highly Underrated)

Index only **subset of rows**

```sql
CREATE INDEX idx_active_users
ON users(email)
WHERE is_active = true;
```

##### Why it’s powerful

- Smaller index
    
- Faster scans
    
- Lower write cost
    

**Perfect for**

- Soft deletes
    
- Status flags
    

**Interview**

> “Partial indexes encode business logic into storage.”


#### 8️⃣ Functional Index

Index on expression

```sql
CREATE INDEX idx_lower_email
ON users(LOWER(email));
```

Required for:

```sql
WHERE LOWER(email) = 'a@b.com';
```

**Interview**

> “Postgres does not automatically apply functions to indexed columns.”

---

##### 9️⃣ Indexes & JOINs

Postgres uses index on:

- FK side usually
    
- Join condition
    

```sql
SELECT *
FROM orders o
JOIN users u ON o.user_id = u.id;
```

Indexes needed:

- `users(id)` (PK)
    
- `orders(user_id)` (FK)
    

**Interview**

> “Indexes on foreign keys dramatically reduce join cost.”

---

###### 🔟 Indexes & Writes (The Dark Side)

##### Every index causes:

- Extra disk writes
    
- Slower INSERT/UPDATE/DELETE
    
- More vacuum work
    

Especially bad for:

- High-write systems
    
- GIN indexes
    

**Rule**

> Indexes optimize reads, not writes.

---

#### 1️⃣1️⃣ Vacuum, Bloat & Reindexing

Postgres uses **MVCC**

Effects:

- Dead tuples accumulate
    
- Index bloat occurs
    

Tools:

- `VACUUM`
    
- `VACUUM ANALYZE`
    
- `REINDEX`
    

**Interview**

> “Indexes also suffer from bloat and require maintenance.”

---

#### 1️⃣2️⃣ How to Know If Index Is Used

```sql
EXPLAIN ANALYZE SELECT ...
```

Look for:

- `Index Scan`
    
- `Bitmap Index Scan`
    
- `Seq Scan` ❌
    

**Red flags**

- `Seq Scan` on huge table
    
- High actual vs estimated rows
    

---

#### 1️⃣3️⃣ When NOT to Use Indexes

❌ Very small tables  
❌ Columns with few distinct values  
❌ Heavy write workloads  
❌ Queries using functions without functional index

**Interview**

> “Over-indexing can kill write throughput.”

---

##### 1️⃣4️⃣ Common US Remote Interview Questions

#### Q: Why isn’t my index used?

Answer:

- Low selectivity
    
- Wrong column order
    
- Function applied
    
- Outdated statistics
    
- Planner cost choice
    

---

#### Q: Difference between B-tree and GIN?

Answer:

- B-tree: scalar, ordered, range queries
    
- GIN: multi-value, inverted mapping, expensive writes
    

---

#### Q: What is index-only scan?

Answer:

- Query served entirely from index
    
- Requires visibility map
    

---

#### Q: How many indexes is too many?

Answer:

- Depends on write rate and query patterns
    
- Measure via `EXPLAIN ANALYZE`
    

---

#### 1️⃣5️⃣ Final Mental Checklist (Memorize This)

- Index = tradeoff
    
- Planner decides, not you
    
- Column order matters
    
- Partial + covering indexes = power tools
    
- Always verify with `EXPLAIN ANALYZE`
    

---

If you want, next we can:

- Analyze **real slow queries**
    
- Design **index strategy for a large system**
    
- Compare **Postgres vs MySQL indexing**
    
- Practice **mock interview questions**
    

Just tell me where to go next.


Forget definitions. You need this **intuition**:

- An index is a **separate data structure** that:
    
    - trades **write cost + storage** for **faster reads**
        
    - avoids scanning the entire table
        
- Most relational DBs use **B-tree indexes**
    
- Index ≠ faster always
    

Key idea:

> **Indexes optimize access paths, not queries**

---

##### 2️⃣ When Indexes Help (and When They Don’t)

##### ✔️ Indexes help when:

- Filtering reduces rows **significantly**
    
- Columns have **high cardinality**
    
- Used in:
    
    - `WHERE`
        
    - `JOIN`
        
    - `ORDER BY`
        
    - `GROUP BY`
        

##### ❌ Indexes don’t help when:

- Small tables
    
- Low-selectivity columns (`status`, `is_active`)
    
- Query returns **most of the table**
    
- Heavy write workloads
    

Interview phrase:

> “If a query touches a large percentage of rows, a sequential scan is often cheaper.”

---

##### 3️⃣ Cardinality & Selectivity (VERY IMPORTANT)

###### Cardinality

- Number of **distinct values** in a column
    

###### Selectivity

- How much data is filtered out
    

Examples:

- `user_id` → high cardinality → great index
    
- `gender`, `status` → low cardinality → often bad index
    

But:

- Composite indexes can make low-cardinality columns useful
    

---

###### 4️⃣ Types of Indexes You Should Know

You don’t need every exotic one — just these:

###### 🔹 B-tree (default)

- Works for:
    
    - equality
        
    - range queries
        
    - sorting
        
- Most common
    

##### 🔹 Composite (multi-column)

- Order matters (**leftmost prefix rule**)
    
- `(user_id, created_at)` ≠ `(created_at, user_id)`
    

##### 🔹 Unique index

- Enforces uniqueness
    
- Used by optimizer for faster lookups
    

###### 🔹 Partial index

- Index only a subset of rows
    
- Example: only `WHERE is_deleted = false`
    

> System design win: “Use partial indexes for hot paths.”

---

###### 5️⃣ Leftmost Prefix Rule (Must Know)

For index `(a, b, c)`:

|Query|Uses index?|
|---|---|
|`WHERE a = ?`|✅|
|`WHERE a = ? AND b = ?`|✅|
|`WHERE b = ?`|❌|
|`WHERE a = ? AND c = ?`|⚠️ partially|

This alone explains **80% of “why index not used” questions**.

---

##### 6️⃣ Covering Indexes

##### What it means

- Query can be answered **entirely from index**
    
- No table lookup needed
    

Example:

```sql
INDEX (user_id, created_at, status)
```

Query:

```sql
SELECT created_at, status FROM orders WHERE user_id = ?
```

🔥 Huge performance gain

Interview phrase:

> “Covering indexes reduce random I/O.”

---

#### 7️⃣ Indexes & JOIN Performance

You MUST know this:

- Foreign keys **should almost always be indexed**
    
- Join performance depends on:
    
    - join type
        
    - data distribution
        
    - index presence on join columns
        

Bad:

```sql
orders.user_id NOT indexed
```

Good:

```sql
INDEX orders(user_id)
```

---

##### 8️⃣ ORDER BY, GROUP BY & Indexes

##### ORDER BY

- Index can avoid sorting
    
- Must match index order
    
- `ORDER BY created_at DESC` needs matching index direction (DB-specific)
    

##### GROUP BY

- Index can help grouping
    
- But aggregation still costs
    
- Often combined with filtering
    

---

##### 9️⃣ Pagination & Indexing (Interview Favorite)

##### OFFSET pagination problem

```sql
LIMIT 20 OFFSET 100000
```

Why bad:

- DB still scans skipped rows
    

##### Keyset pagination (index-friendly)

```sql
WHERE created_at < last_seen
ORDER BY created_at DESC
LIMIT 20
```

Index required:

```sql
(created_at DESC)
```

---

##### 🔟 Query Planner & Execution Plan

You don’t need syntax — you need **interpretation**.

##### Planner chooses:

- Sequential scan
    
- Index scan
    
- Bitmap scan
    

Why index not used:

- Table too small
    
- Poor selectivity
    
- Outdated statistics
    
- Cost estimation says seq scan is cheaper
    

Key mindset:

> DB optimizer is cost-based, not rule-based.

---

#### 1️⃣1️⃣ Write Cost of Indexes (Tradeoff)

Each index:

- Slows down:
    
    - INSERT
        
    - UPDATE
        
    - DELETE
        
- Consumes memory
    
- Needs maintenance
    

Design insight:

> “I only index queries that are on the critical path.”

---

#### 1️⃣2️⃣ Index Maintenance Issues

Things that break performance over time:

- Index bloat
    
- Fragmentation
    
- Stale statistics
    
- Skewed data
    

Operational concepts:

- Vacuum / Analyze (conceptually)
    
- Reindexing (rare but real)
    

---

#### 1️⃣3️⃣ Common Performance Killers

You should recognize these instantly:

- Functions on indexed columns
    
    ```sql
    WHERE DATE(created_at) = ...
    ```
    
- Type mismatch
    
- `LIKE '%abc'`
    
- `OR` conditions across columns
    
- Implicit casts
    

---

#### 1️⃣4️⃣ Measuring Performance Correctly

You should know:

- Why benchmarking locally lies
    
- Cache effects
    
- Cold vs warm cache
    

Correct mindset:

> Measure with realistic data and access patterns.

---

##### 1️⃣5️⃣ System Design Framing (VERY IMPORTANT)

How to talk in interviews:

> “I start by identifying hot queries, look at access patterns, then design indexes around those queries. I balance read performance against write amplification.”

That sentence alone passes many interviews.

---

##### Final Checklist (Short Version)

If you can explain:

- ✔️ Cardinality & selectivity
    
- ✔️ Composite index ordering
    
- ✔️ Covering indexes
    
- ✔️ Index vs seq scan tradeoff
    
- ✔️ Pagination strategies
    
- ✔️ Write cost of indexes
    
- ✔️ Why index is not used
    

You **know indexing at a senior level**.

---

If you want, next we can:

- Convert this into **clean markdown notes**
    
- Walk through **real-world query + index design**
    
- Do **mock interview questions**
    
- Or tie this directly to **Postgres internals** (since you were asking about that earlier)
    

Just say the word.
This should be your **largest section**.

#### 🔹 Index fundamentals (not definitions)

- B-tree vs Hash (why B-tree wins)
    
- Composite index ordering (leftmost prefix rule)
    
- Covering indexes
    

#### 🔹 Index selection strategy

- High-cardinality columns
    
- Indexing foreign keys
    
- Indexing for `WHERE`, `JOIN`, `ORDER BY`
    

#### 🔹 Read vs write tradeoff

- Insert/update cost
    
- Index bloat
    
- Too many indexes problem
    

#### 🔹 Query planning

- Sequential scan vs index scan
    
- Why index is sometimes NOT used
    
- `EXPLAIN` / `EXPLAIN ANALYZE` mindset

##### Phase 6 – PostgreSQL vs NoSQL (hybrid thinking)

Postgres supports:

`JSONB ARRAY ENUM RANGE UUID`

You can do:

`metadata JSONB`

Meaning:  
👉 You don’t lose flexibility  
👉 You gain constraints + transactions


##### Phase 3 – Querying the relational way

###### 5️⃣ JOINs are not expensive (when done right)

`SELECT u.email, p.title FROM users u JOIN posts p ON p.user_id = u.id;`

Postgres:

- Uses indexes
    
- Builds execution plans
    
- Chooses hash join / nested loop / merge join
    

Mongo:

- You manually optimize `$lookup`
    

---

###### 6️⃣ Aggregation (Postgres destroys NoSQL here)

`SELECT user_id, COUNT(*)  FROM posts GROUP BY user_id HAVING COUNT(*) > 10;`

This runs **inside the engine**, optimized in C.


##### Phase 2 – Data modeling (THIS is where NoSQL users struggle)

###### 3️⃣ Tables are contracts, not suggestions

`CREATE TABLE users (   id UUID PRIMARY KEY,   email TEXT NOT NULL UNIQUE,   password_hash TEXT NOT NULL,   created_at TIMESTAMP DEFAULT now() );`

Key ideas:

- `NOT NULL` is enforcement, not documentation
    
- `UNIQUE` is a **guarantee**
    
- Types matter a lot
    

This replaces:

`if (!email) throw Error();`

---

###### 4️⃣ Relationships (the big shift)

Mongo:

`{   "userId": "...",   "posts": [...] }`

Postgres:

`CREATE TABLE posts (   id UUID PRIMARY KEY,   user_id UUID REFERENCES users(id),   title TEXT NOT NULL );`

This gives you:

- Referential integrity
    
- Cascades
    
- Guarantees no orphan records


###### **Data Integrity & Safety**

- Primary keys vs unique constraints
    
- Foreign keys (when to avoid them)
    
- Cascades (delete/update dangers)
    
- Check constraints vs application validation













