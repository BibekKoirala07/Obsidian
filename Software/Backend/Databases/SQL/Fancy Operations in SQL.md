### Fancy Operations
Here are essential SQL and database terms that will make you sound knowledgeable in technical interviews:

**Operations & Properties**

- **Idempotent** - operations that produce the same result no matter how many times you execute them
- **Atomic** - transactions that complete fully or not at all
- **Deterministic** - functions that always return the same output for the same input
- **Commutative** - operations where order doesn't matter (A + B = B + A)
- **Immutable** - data that cannot be changed after creation
- **Transactional** - operations grouped into all-or-nothing units
- **Reentrant** - code that can be safely executed concurrently

**Performance & Optimization**

- **Cardinality** - the uniqueness of data values (high cardinality = many unique values)
- **Selectivity** - the fraction of rows returned by a query condition
- **Predicate pushdown** - filtering data as early as possible in query execution
- **Materialized view** - pre-computed query results stored physically
- **Query plan** - the execution strategy the database chooses
- **Index selectivity** - how well an index narrows down results
- **Table scan** vs **Index seek** - full table read vs targeted index lookup
- **Covering index** - an index containing all columns needed for a query
- **Clustered** vs **Non-clustered index** - physical vs logical data ordering
- **Denormalization** - intentionally introducing redundancy for performance
- **Partitioning** - dividing tables into smaller, manageable pieces
- **Sharding** - horizontal partitioning across multiple database instances

**Data Integrity & Constraints**

- **Referential integrity** - maintaining valid relationships between tables
- **Constraint propagation** - how constraints affect related data
- **Cascading operations** - automatic actions on related records (CASCADE DELETE)
- **Orphaned records** - child records without valid parent references
- **Surrogate key** - artificial identifier (like auto-increment ID)
- **Natural key** - real-world unique identifier
- **Composite key** - primary key made of multiple columns
- **Foreign key constraint** - enforcing relationships between tables

**Concurrency & Transactions**

- **ACID properties** - Atomicity, Consistency, Isolation, Durability
- **Isolation levels** - READ UNCOMMITTED, READ COMMITTED, REPEATABLE READ, SERIALIZABLE
- **Dirty read** - reading uncommitted changes from another transaction
- **Phantom read** - new rows appearing between reads in same transaction
- **Non-repeatable read** - data changing between reads in same transaction
- **Deadlock** - two transactions waiting for each other's locks
- **Pessimistic locking** - locking data before reading/writing
- **Optimistic locking** - checking for conflicts at commit time
- **Two-phase commit** - protocol for distributed transactions
- **Write-ahead logging (WAL)** - recording changes before applying them

**Advanced Concepts**

- **Normalization** - organizing data to reduce redundancy (1NF, 2NF, 3NF, BCNF)
- **Temporal tables** - tracking historical data changes over time
- **Window functions** - calculations across related rows (ROW_NUMBER, RANK, LEAD, LAG)
- **Common Table Expressions (CTEs)** - named temporary result sets
- **Recursive queries** - queries that reference themselves
- **Pivoting** and **Unpivoting** - transforming rows to columns and vice versa
- **Set-based operations** - thinking in terms of entire datasets rather than row-by-row
- **Lateral joins** - correlated subqueries in FROM clause
- **Correlated subquery** - subquery referencing outer query columns
- **Derived table** - subquery in FROM clause
- **Scalar subquery** - subquery returning single value

**Architecture & Design**

- **Schema** - logical organization of database objects
- **Star schema** vs **Snowflake schema** - data warehouse designs
- **OLTP** (Online Transaction Processing) - operational databases
- **OLAP** (Online Analytical Processing) - analytical/reporting databases
- **Data lake** vs **Data warehouse** - raw vs structured analytical storage
- **ETL** (Extract, Transform, Load) - data integration process
- **Replication** - copying data across database instances
- **Master-slave architecture** - primary writer with read replicas
- **Eventual consistency** - distributed systems reaching consistency over time
- **CAP theorem** - tradeoffs between Consistency, Availability, Partition tolerance

**Query Techniques**

- **Subquery factoring** - using CTEs for readability
- **Query optimization** - improving query performance
- **Execution plan analysis** - understanding how queries run
- **Index hints** - forcing specific index usage
- **Statistical analysis** - database stats for query planning
- **Parameterized queries** - using placeholders for SQL injection prevention
- **Prepared statements** - pre-compiled SQL for performance

Using these terms appropriately in context will demonstrate deep SQL knowledge in interviews. Good luck with your remote job search!
Got it.  
You don’t want **definitions like a textbook** — you want **vocabulary that signals seniority**, that you can _say naturally_ in interviews or while teaching.

Below is a **curated, high-signal SQL / database vocabulary list** with **one-line meanings** and **how it sounds in interviews**.

Think of this as your **SQL language arsenal**.

---

##### 🔑 CORE CONCEPT WORDS (must-know)

###### **Idempotent**

> An operation that can be applied multiple times with the same result.

🗣 Interview use:

> “This migration is idempotent, so rerunning it is safe.”

---

###### **Deterministic**

> Same input → same output, always.

🗣:

> “This function is deterministic, which helps the planner optimize it.”

---

###### **Side effects**

> Changes state beyond returning data.

🗣:

> “SELECTs should be side-effect free.”

---

###### **Atomic**

> All-or-nothing execution.

🗣:

> “The operation is atomic due to transactional guarantees.”

---

###### **Consistency (ACID)**

> Data always moves from one valid state to another.

🗣:

> “Constraints and triggers enforce consistency.”

---

###### **Isolation**

> Transactions don’t interfere with each other.

🗣:

> “We rely on MVCC for isolation.”

---

###### **Durability**

> Once committed, data survives crashes.

🗣:

> “WAL ensures durability.”

---

##### 🧠 QUERY & PLANNER WORDS (INTERVIEW GOLD)

###### **Query planner / optimizer**

> Component that decides _how_ to execute SQL.

🗣:

> “The planner chose a hash join due to row estimates.”

---

###### **Cost-based optimization**

> Planner chooses plans based on estimated cost.

🗣:

> “Postgres uses a cost-based planner, not rule-based.”

---

###### **Selectivity**

> How much data a filter removes.

🗣:

> “High-selectivity indexes are more effective.”

---

##### **Cardinality**

> Estimated number of rows.

🗣:

> “Bad cardinality estimates can cause bad plans.”

---

###### **Predicate pushdown**

> Filters applied as early as possible.

🗣:

> “The planner pushed down the WHERE clause.”

---

###### **Inlining**

> Expanding a view or function into the main query.

🗣:

> “Views are inlined by the optimizer.”

---

###### **Execution plan**

> Step-by-step query execution strategy.

🗣:

> “We inspected the execution plan using EXPLAIN.”

---

###### 📦 STORAGE & DATA WORDS

###### **Heap table**

> Unordered row storage.

🗣:

> “Materialized views are stored as heap tables.”

---

##### **Tuple**

> A single row (Postgres term).

🗣:

> “Dead tuples need vacuuming.”

---

###### **Page**

> Fixed-size disk block (usually 8KB).

🗣:

> “Indexes reduce page reads.”

---

##### **Bloat**

> Wasted space from dead tuples.

🗣:

> “Frequent updates caused table bloat.”

---

###### **HOT update**

> Heap-Only Tuple update (Postgres).

🗣:

> “HOT updates avoid index rewrites.”

---

###### **Visibility**

> Whether a row is visible to a transaction.

🗣:

> “MVCC controls row visibility.”

---

###### 🧪 TRANSACTION & CONCURRENCY WORDS

###### **MVCC**

> Multi-Version Concurrency Control.

🗣:

> “MVCC avoids read-write blocking.”

---

###### **Snapshot**

> Transaction’s view of the database.

🗣:

> “Each transaction operates on a snapshot.”

---

###### **Write amplification**

> One write causing many internal writes.

🗣:

> “Materialized view refresh causes write amplification.”

---

###### **Lock escalation**

> Small locks becoming larger ones.

🗣:

> “Lock escalation can hurt concurrency.”

---

###### **Deadlock**

> Circular waiting between transactions.

🗣:

> “We avoided deadlocks by consistent lock ordering.”

---

###### 🧱 SCHEMA & DESIGN WORDS

###### **Normalization**

> Reducing redundancy.

🗣:

> “OLTP schemas are usually normalized.”

---

###### **Denormalization**

> Intentional redundancy for performance.

🗣:

> “Materialized views are a form of denormalization.”

---

###### **Projection**

> Selecting specific columns.

🗣:

> “Views provide controlled projections.”

---

##### **Invariants**

> Rules that must always hold.

🗣:

> “Triggers enforce invariants.”

---

###### **Contract**

> Stable interface between systems.

🗣:

> “Views act as a schema contract.”

---

###### **Backward compatibility**

> Changes don’t break existing consumers.

🗣:

> “Views help maintain backward compatibility.”

---

###### ⚙️ INDEXING WORDS

###### **Covering index**

> Index that satisfies query without touching table.

🗣:

> “This index is covering for the query.”

---

###### **Index-only scan**

> Reads only the index.

🗣:

> “Visibility map enabled index-only scans.”

---

###### **Partial index**

> Index on subset of rows.

🗣:

> “Partial indexes improve selectivity.”

---

###### **Composite index**

> Index on multiple columns.

🗣:

> “Column order matters in composite indexes.”

---

##### 🧰 VIEWS & MATERIALIZED VIEWS WORDS

###### **Read façade**

> Read-only abstraction layer.

🗣:

> “Views act as read façades.”

---

###### **Staleness**

> Data not fully up to date.

🗣:

> “Materialized views trade freshness for speed.”

---

###### **Refresh strategy**

> How and when data is updated.

🗣:

> “We use concurrent refresh to avoid blocking.”

---

###### **Precomputation**

> Calculating results ahead of time.

🗣:

> “Materialized views rely on precomputation.”

---

##### 🔐 SECURITY WORDS

###### **Least privilege**

> Minimum required permissions.

🗣:

> “We grant SELECT only on views.”

---

####### **Security boundary**

> Controlled access layer.

🗣:

> “Views create a security boundary.”

---

##### **Privilege escalation**

> Gaining unintended permissions.

🗣:

> “Procedures prevent privilege escalation.”

---

##### 🧠 ADVANCED / ARCHITECTURE WORDS (VERY IMPRESSIVE)

##### **CQRS**

> Separate read and write models.

🗣:

> “Views fit naturally into CQRS.”

---

###### **OLTP vs OLAP**

> Transactional vs analytical workloads.

🗣:

> “Materialized views are OLAP-oriented.”

---

###### **Eventual consistency**

> Data converges over time.

🗣:

> “Dashboards tolerate eventual consistency.”

---

##### **Write path / Read path**

> How data flows for writes vs reads.

🗣:

> “We optimized the read path using materialized views.”

---

###### **Operational complexity**

> Ongoing maintenance cost.

🗣:

> “Triggers increase operational complexity.”

---

#### 🧠 ONE FINAL TIP (VERY IMPORTANT)

You don’t need to **memorize everything**.

You need to:

- Understand them
    
- Use them _naturally_
    
- Drop them **casually**, not forcefully
    

💬 Example:

> “We used materialized views to precompute aggregates, accepting some staleness, and handled refreshes concurrently to avoid blocking reads.”

That sentence alone sounds **US-remote-senior-level**.