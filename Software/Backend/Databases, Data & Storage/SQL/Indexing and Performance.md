### Indexing and Performance
Simply,==Indexing is the separate data structure which helps DB to find row faster, not display them(common confusion)==. That is it. It is like a look up structure. Without index, DB is forced to do sequential scan. With indexing, DB can do Index scan/Index jump(jumping directly to rows).

Index trade off write speed + storage for read speed. 

###### 2. How slow queries happen (the real enemy)

 Bad performance usually comes from:

1. Full table scans on large tables
    
2. Wrong index (exists but not usable)
    
3. Too many rows returned
    
4. Poor cardinality (low selectivity)
    
5. Planner choosing a bad plan (stats problem)
    

Indexes solve **#1 and #2**, partially help **#3**, and rely on **stats** for #5.

Indexes are mainly used in `WHERE`, `JOIN`, `ORDER BY` and `GROUP BY`.
###### Let's start for `WHERE`.
We can create index like this
```sql
	CREATE INDEX ON users(email)
```
and if we use email to query like
```sql
	EXPLAIN ANALYZE SELECT * FROM users WHERE email = "example@gmail.com"
```
Even with common sense, We can see that `email` is indexed. Without indexing, we would know that that query would do sequential scan but with indexing, DB could jump directly to matching rows. 

Now we may think what about composite indexing ? 
```sql
	CREATE INDEX idx_name ON users(username, email)
```
The tricky thing to understand is the this indexing works for `username` alone and `username` and `email`. It doesn't work for email at all as index starts with `username`.
```sql
	SELECT * FROM users WHERE username = 'username' // correct
	SELECT * FROM users WHERE username = 'username' and email = 'email' // it works
	
	SELECT * FROM users WHERE email = 'email' // it doesnot work as indexing starts with username
	SELECT * FROM users WHERE email = 'email' and username = 'username' // it doesnot work
```
DB can't jump using `email` first. It is called ==Left most rule==.

If you want to use both separately i.e. meaning just `email` or `username`, then index both separately. 

==One composite index != two separate index==
###### Let's look at `JOIN`
Now let's look at another indexing in query. 
Let's try to join user with order
```sql
	EXPLAIN SELECT * FROM orders
	JOIN users
	ON users.id = orders.user_id
```
Now let's look from the point of common sense. We know that PK is already indexed. so `users.id` is indexed but is `orders.user_id` indexed ? Well no, So it goes like this : `For each order, DB will search for users.id again and again`. 
But what if we create Index in `user_id`.
```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```
Now DB can often avoid sequential scans and use more efficient plans but it still don't mean that DB will escape sequential scan. 
###### Let's look at `ORDER BY`
Let us have a query
```sql
	EXPLAIN SELECT * FROM orders
	ORDER BY created_at DESC
```
First DB reads all the row and sort it.  So it causes full table scan and expensive sort operation. 

So we need to create the index in `created_at DESC`
```sql
	CREATE INDEX createdat_desc ON users(created_at DESC)
```
Now DB think that Rows are already sorted in the index. 

Now let's move forward by concatenating `WHERE` and `ORDER BY`
```sql
	EXPLAIN SELECT * FROM orders
	WHERE user_id = 5
	ORDER BY created_at DESC
	LIMIT 10
```
Without index what do you think would happen ? Well first DB will filter all the rows, after that sorting will happen and will apply limit. But with indexing
```sql
	CREATE INDEX double_index ON orders(user_id, created_at DESC)
```
Not it works for above query. Now DB filters by `user_id`, rows are already sorted and applies `limit`

If we see `ORDER BY` and `LIMIT`, then there should be index. 
###### Let's look at `GROUP BY`
The things with indexing with `GROUP BY` is that it depends on query and data only. Indexing can by help but not always. 

Let look with example
```sql
	EXPLAIN SELECT user_id, COUNT(*) FROM orders GROUP BY user_id
```
First DB have to get all the orders and group by `user_id`. Using Explain we can see 
```sql
Seq Scan
HashAggregate // slow
```

So what does adding indexing on `user_id` does ? Now it will show
```
Index Scan
GroupAggregate
```
But ==there is some caveat as it only work sometimes==.
It only can help when 
- Grouping Column has  many distinct values
- Query can read data in grouped order
- Index allows index-only scan
But they don't help when 
- There are very few distinct values 
- DB still needs to scan most rows 
- Hash Aggregation is cheaper. 

==In real world. If WHERE and GROUP BY comes in sequential, then do indexing on both==. 


Index is a separate Data Structure that maps column values to physical row location(TIDs)(just stores column values and where to find the rows in the table). 

Since PG stores table rows in block called pages(usually 8 KB each). Table = a bunch of these pages(like sheet in a notebook). And Index is stored separately from table data. It has its own page, organized for fast search. Index = "table of content" page.

Index stores: key(the column value you are indexing `email`) -> (block id(which page in the table contains the row), offset(which row in that page)).

Another thing is index never stores the full row(It is like table of content). It never store full row but there are exception like covering indexes(INCLUDE), which store extra columns in the index so PG can sometimes skip the table entirely. 

###### B-tree Index
###### Hash index
##### GIN Index
###### Gist Index
###### BRIN Index
###### Composite/Multi-Column Index
###### Partial Index
###### Functional Index
###### Covering(INCLUDE)
It skip entire table entirely. 


==TIPS==
- Indexing should be done in high cardinality columns as index performs best in those columns. Like it shouldn't be done in columns like `gender` as it has low cardinality. It should be thing like `email` or things like that with high cardinality. What about composite index ? Well have low cardinality column in second option. Like `users(email, isActive)`.


###### 2️⃣ When PostgreSQL Uses an Index

Postgres decides via the **query planner**.

###### Typical cases where index is used

- `WHERE column = value`
    
- `WHERE column IN (...)`
    
- `WHERE column >, <, BETWEEN`
    
- `ORDER BY` (sometimes)
    
- `JOIN` conditions
    
- `UNIQUE` constraint checks
    

###### Cases where index is often NOT used

- Small tables (sequential scan cheaper)
    
- Very low selectivity (`is_active = true` when 95% rows are true)
    
- Functions on indexed column:
    
    ```sql
    WHERE LOWER(email) = 'a@b.com'  -- unless functional index exists
    ```
    
- `LIKE '%abc'` (leading wildcard)
    

**Interview line**

> “Postgres may ignore an index if the cost of random I/O exceeds sequential scan cost.”

---

###### 3️⃣ Index Scan vs Bitmap Index Scan (VERY IMPORTANT)

###### Index Scan

- Used when **few rows** expected
    
- Reads index → jumps to heap rows
    

###### Bitmap Index Scan

- Used when **many rows**
    
- Builds a bitmap of matching rows
    
- Reads heap pages sequentially
    

```sql
EXPLAIN ANALYZE SELECT * FROM orders WHERE status = 'PENDING';
```

**Interview gold**

> “Bitmap scans reduce random I/O by batching heap access.”

---

###### 4️⃣ Core Index Types You MUST Know

###### 4.1 B-Tree (Default, 90% use cases)

###### Supports

- `=`
    
- `<, >, BETWEEN`
    
- `ORDER BY`
    
- `MIN / MAX`
    

```sql
CREATE INDEX idx_users_email ON users(email);
```

**Key properties**

- Balanced tree
    
- Log(N) lookup
    
- Sorted order
    

**Interview**

> “B-tree is optimal for range queries and ordering.”

---

###### 4.2 Hash Index (Rare)

- Only supports `=`
    
- No ordering
    
- Historically unsafe (pre PG10)
    

```sql
CREATE INDEX ON users USING HASH(email);
```

👉 **Almost never used**  
B-tree is better in most cases.

---

###### 4.3 GIN (Generalized Inverted Index)

Used for **multi-valued columns**

###### Common uses

- `ARRAY`
    
- `JSONB`
    
- Full-text search (`tsvector`)
    

```sql
CREATE INDEX idx_tags ON posts USING GIN(tags);
```

```sql
WHERE tags @> ARRAY['postgres']
```

**Tradeoff**

- Very fast reads
    
- Very expensive writes
    

**Interview**

> “GIN indexes invert the mapping: value → rows.”

---

###### 4.4 GiST (Generalized Search Tree)

Used for **geometric / fuzzy matching**




### EXPLAIN AND EXPLAIN ANALYZE
#### Use cases

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

---

#### 5️⃣ Composite (Multi-Column) Indexes

```sql
CREATE INDEX idx_orders_user_status
ON orders(user_id, status);
```

###### Left-most prefix rule

Index usable for:

- `user_id`
    
- `user_id + status`
    

❌ NOT usable for:

- `status` alone
    

**Interview**

> “Column order matters in composite indexes.”

---

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

---

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


##### Phase 5 – Indexes (performance fundamentals)

###### 9️⃣ Index types (huge topic)

- B-tree (default)
    
- Hash
    
- GIN (arrays, JSONB)
    
- GiST (geo, ranges)
    
- Partial indexes
    
- Composite indexes
    

Example:

`CREATE INDEX idx_users_email ON users(email);`

Bad indexing = slow DB even with good SQL.
### Indexing with Trees
