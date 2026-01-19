##### Complete PostgreSQL Query Execution Path - Deep Dive

Let me walk you through the **entire journey** of a query from connection establishment to final result return. This is the comprehensive path you need.

##### Phase 1: Connection Establishment

###### Initiates Connection

When you run `psql -U bibek -d mydb`, here's what happens:

**Step 1: DNS Resolution & Network Layer**

- Client resolves the PostgreSQL server hostname to an IP address
- Client initiates a TCP three-way handshake (SYN, SYN-ACK, ACK) to the PostgreSQL port (default 5432)
- TCP connection is established at the transport layer

**Step 2: PostgreSQL Listener (Postmaster)**

- The **postmaster** process (main PostgreSQL daemon) is listening on the configured port
- It accepts the incoming TCP connection
- Postmaster reads `postgresql.conf` for connection parameters:
    - `max_connections`: checks if connection limit reached
    - `listen_addresses`: verifies connection is from allowed address
    - `port`: the listening port
- Postmaster reads `pg_hba.conf` (Host-Based Authentication) to determine authentication method for this connection

**Step 3: Backend Process Creation**

- Postmaster **forks a new backend process** (postgres process) dedicated to this client connection
- This backend process inherits the TCP socket connection
- Each client connection gets its own dedicated backend process (or uses a connection from the pool if using pgBouncer/similar)

###### 1.2 Authentication Phase

**Step 4: Authentication Challenge** Based on `pg_hba.conf` entry, PostgreSQL determines authentication method (md5, scram-sha-256, trust, peer, etc.)

For password-based authentication (md5/scram-sha-256):

- Server sends authentication challenge to client
- Client receives username "bibek" and password from user
- Client hashes the password using the specified method
- Client sends username "bibek" + hashed password back to server

**Step 5: Credential Verification**

- Backend process queries `pg_authid` system catalog (or `pg_shadow` view)
- Looks up the role "bibek" and retrieves stored password hash
- Compares received hash with stored hash
- If match: authentication succeeds
- If no match: connection terminates with authentication error

**Step 6: Role Validation**

- Checks if role "bibek" has `LOGIN` privilege (`rolcanlogin` in `pg_authid`)
- Checks if role is not expired (no `VALID UNTIL` date passed)
- Checks connection limits for this role (`CONNECTION LIMIT`)

###### 1.3 Session Initialization

**Step 7: Database Connection**

- User specified database "mydb" in connection string
- Backend verifies role "bibek" has `CONNECT` privilege on database "mydb"
- Queries `pg_database` catalog to verify database exists
- Checks `datallowconn` flag (database allows connections)
- Loads database-specific configuration parameters

**Step 8: Session Setup**

- Backend initializes session memory context
- Sets session variables (timezone, datestyle, client_encoding, etc.)
- Loads default search_path (usually `"$user", public`)
- Initializes transaction state (initially in no active transaction)
- Allocates shared memory structures for this backend
- Registers backend in `pg_stat_activity`

Connection is now **ESTABLISHED** and ready to receive queries.

---

#### Phase 2: Query Reception & Parsing

###### 2.1 Query Arrival

**Step 9: Client Sends Query** Let's say you execute:

```sql
SELECT id, name FROM users WHERE age > 25;
```

- Client sends query string over TCP connection using PostgreSQL wire protocol
- Backend process receives query string from socket buffer
- Query stored in backend's memory buffer

**Step 10: Protocol Handling**

- Backend identifies message type (Simple Query 'Q' or Extended Query 'P'/'B'/'E')
- For simple query protocol: entire SQL string received at once
- Backend allocates memory for query processing

###### 2.2 Parser Stage

**Step 11: Lexical Analysis (Scanner)**

- **Lexer/Scanner** breaks query into tokens
- Identifies keywords: SELECT, FROM, WHERE
- Identifies identifiers: id, name, users, age
- Identifies operators: >, =
- Identifies literals: 25
- Validates basic syntax (matching quotes, parentheses, etc.)

**Step 12: Syntax Analysis (Parser)**

- **Parser** builds a **Parse Tree** (raw syntax tree) from tokens
- Verifies SQL grammar is correct
- Creates hierarchical structure representing query
- If syntax error: returns error immediately (e.g., "syntax error at or near...")

**Parse Tree structure:**

```
SelectStmt
├── targetList: [id, name]
├── fromClause: [users]
└── whereClause: [age > 25]
```

###### 2.3 Transformation (Semantic Analysis)

**Step 13: Query Rewrite**

- **Analyzer/Transformer** converts Parse Tree to **Query Tree**
- Performs semantic analysis and validation:

**Step 14: Schema Resolution**

- Looks up table "users" in current search_path schemas
- Queries `pg_class` system catalog to find the table OID
- Resolves which schema contains "users" (e.g., public.users)

**Step 15: Permission Checking - Schema Level**

- Checks if role "bibek" has `USAGE` privilege on the schema containing "users"
- Queries `pg_namespace` and checks ACLs
- If no USAGE: "permission denied for schema" error

**Step 16: Permission Checking - Table Level**

- Checks if role "bibek" has `SELECT` privilege on table "users"
- Queries `pg_class.relacl` (table ACL)
- Checks both direct privileges and inherited role privileges
- If no SELECT: "permission denied for table users" error

**Step 17: Column Resolution & Validation**

- Resolves columns: id, name, age
- Queries `pg_attribute` to verify columns exist in table "users"
- Checks column types for type compatibility with operations
- Verifies "age > 25" is valid (age is numeric type)

**Step 18: Permission Checking - Column Level**

- Checks column-level SELECT privileges (if column-level security configured)
- Verifies role "bibek" can access columns id, name, age
- If column-level RLS: applies row-level security policies

**Step 19: View & Rule Expansion**

- If "users" is a VIEW, expands view definition
- Applies any query rewrite RULES defined on the table
- Recursively processes expanded queries

**Step 20: Row-Level Security (RLS)**

- Checks if RLS is enabled on "users" table (`pg_class.relrowsecurity`)
- If enabled: retrieves RLS policies from `pg_policy`
- Applies USING clause policies for SELECT
- Combines policies with query WHERE clause using AND

**Query Tree now contains:**

- Fully qualified table references (schema.table)
- Resolved column references with type information
- Applied security policies
- Expanded views/rules

---

#### Phase 3: Query Planning & Optimization

###### 3.1 Planner Initialization

**Step 21: Planner Entry**

- **Planner/Optimizer** receives Query Tree
- Determines query complexity (simple scan, join, aggregation, etc.)
- Initializes cost estimation structures

**Step 22: Statistics Gathering**

- Queries `pg_statistic` (or `pg_stats` view) for table "users"
- Retrieves:
    - Table row count (`reltuples` from `pg_class`)
    - Table size in pages (`relpages`)
    - Column statistics (most common values, histograms, null fraction)
    - Correlation factors for column ordering
- Uses statistics for cardinality estimation

###### 3.2 Path Generation

**Step 23: Access Path Generation** For table "users", planner generates possible access paths:

**Sequential Scan Path:**

- Cost: `seq_page_cost * num_pages + cpu_tuple_cost * num_rows`
- Reads entire table sequentially
- Applies WHERE filter on each row

**Index Scan Paths:**

- Queries `pg_index` to find indexes on "users" table
- For each relevant index (e.g., index on "age" column):
    - Estimates selectivity of "age > 25" using statistics
    - Calculates index scan cost
    - Considers index type (B-tree, Hash, GiST, etc.)

**Bitmap Scan Path:**

- Combines multiple indexes if available
- Two-phase: bitmap index scan → bitmap heap scan
- Useful for moderate selectivity

**Step 24: Join Path Generation (if query had joins)**

- Would generate nested loop, hash join, merge join paths
- Considers join order permutations
- Estimates join cardinalities

**Step 25: Cost Estimation** For each path, calculates:

- **Startup cost**: cost before first row returned
- **Total cost**: cost to return all rows
- **Estimated rows**: number of rows expected
- Uses cost parameters from `postgresql.conf`:
    - `seq_page_cost`, `random_page_cost`
    - `cpu_tuple_cost`, `cpu_operator_cost`
    - `effective_cache_size`, `work_mem`

###### 3.3 Plan Selection

**Step 26: Best Path Selection**

- Compares total costs of all generated paths
- Selects path with **lowest total cost**
- Considers cheapest startup cost if LIMIT present

**Step 27: Plan Tree Creation**

- Converts chosen path to **Plan Tree** (execution plan)
- Plan nodes represent physical operators:
    - SeqScan, IndexScan, BitmapScan
    - NestLoop, HashJoin, MergeJoin
    - Sort, Aggregate, Limit, etc.

**Example Plan Tree:**

```
SeqScan on users
  Filter: (age > 25)
  Output: id, name
```

Or if using index:

```
IndexScan using users_age_idx on users
  Index Cond: (age > 25)
  Output: id, name
```

---

#### Phase 4: Query Execution

##### 4.1 Executor Initialization

**Step 28: Executor Setup**

- **Executor** receives Plan Tree
- Initializes execution state structures
- Allocates memory for sort/hash operations (from `work_mem`)
- Sets up tuple slot structures for row buffers

**Step 29: Transaction Handling** If no explicit transaction:

- Starts implicit transaction with `BEGIN`
- Assigns Transaction ID (XID) from sequence
- Initializes transaction state in backend's memory

If already in transaction block:

- Uses existing transaction context

**Step 30: Snapshot Acquisition (MVCC)**

- **Snapshot Manager** creates a snapshot for this query
- Snapshot captures:
    - Current transaction ID (xmin)
    - List of active transaction IDs (xip array)
    - Latest committed transaction ID (xmax)
- This snapshot determines **which row versions are visible** to this query
- Snapshot follows isolation level rules (READ COMMITTED, REPEATABLE READ, etc.)

##### 4.2 Table Access

**Step 31: Storage Manager Interaction**

- Executor requests rows from Storage Manager
- Storage Manager interfaces with Buffer Manager

**Step 32: Buffer Manager**

- Checks **Shared Buffer Pool** (shared_buffers in RAM) for required pages
- Computes page number from table size and scan position

**Buffer Hit (Page in Cache):**

- Returns page from shared buffers directly
- Increments `blks_hit` in `pg_stat_user_tables`

**Buffer Miss (Page not in Cache):**

- Finds free buffer slot (or evicts LRU page if full)
- Reads page from disk (calls kernel read)
- Loads page into shared buffer
- Pins buffer to prevent eviction during use
- Increments `blks_read` in `pg_stat_user_tables`

**Step 33: Page Retrieval** For Sequential Scan:

- Reads pages in order from page 0 to page N
- Uses read-ahead if sequential pattern detected

For Index Scan:

- Traverses B-tree index structure:
    - Reads index root page
    - Follows pointers to leaf pages
    - Reads matching index tuples
    - Follows TID (tuple identifier) to heap page

##### 4.3 Tuple Processing & MVCC Visibility

**Step 34: Tuple Access** Each page contains multiple tuples (rows). For each tuple:

**Step 35: MVCC Visibility Check**

- Reads tuple header metadata:
    - `xmin`: transaction ID that inserted this tuple
    - `xmax`: transaction ID that deleted/updated this tuple (0 if not deleted)
    - `cmin/cmax`: command IDs within transaction
    - Infomask bits (committed, aborted, etc.)

**Visibility Rules (for READ COMMITTED):**

- If `xmin` > current snapshot's xmax: **INVISIBLE** (inserted by future transaction)
- If `xmin` in active transaction list: **INVISIBLE** (inserted by uncommitted transaction)
- If `xmin` committed AND `xmax` == 0: **VISIBLE** (valid current version)
- If `xmax` set AND `xmax` committed AND `xmax` < snapshot xmax: **INVISIBLE** (deleted version)
- If `xmax` set AND `xmax` in active list: **VISIBLE** (delete not committed)

**Step 36: Transaction Status Check (CLOG)** For unknown transaction states:

- Queries **CLOG** (Commit Log) in `pg_xact/` directory
- CLOG tracks commit/abort status of transactions
- Returns committed, aborted, or in-progress status
- Result cached for performance

**Step 37: Hint Bits**

- After checking CLOG, sets hint bits in tuple header
- `HEAP_XMIN_COMMITTED`, `HEAP_XMAX_COMMITTED`, etc.
- Avoids future CLOG lookups for same tuple
- Marks page as dirty if hint bits changed

##### 4.4 Row Filtering & Processing

**Step 38: WHERE Clause Evaluation** For each visible tuple:

- Extracts column values (id, name, age)
- Evaluates WHERE condition: `age > 25`
- If condition FALSE: discards tuple
- If condition TRUE: proceeds to next step

**Step 39: Projection**

- Selects only requested columns (id, name)
- Discards other columns (age, etc.)
- Constructs output tuple with selected columns

**Step 40: Tuple Buffering**

- Places output tuple in tuple slot
- If sorting/aggregation needed: stores in work_mem
- Otherwise: prepares for return to client

##### 4.5 Query Modifications (INSERT/UPDATE/DELETE)

For write operations, additional steps occur:

**For INSERT:**

- Allocates new tuple in heap page
- Sets xmin to current transaction ID
- Sets xmax to 0
- Writes tuple to page in buffer

**For UPDATE:**

- Marks old tuple as deleted (sets xmax)
- Creates new tuple version with updated values
- Links tuples via ctid chain (for HOT updates if possible)

**For DELETE:**

- Sets xmax on tuple to current transaction ID
- Tuple remains in heap (not physically deleted yet)

**Step 41: WAL (Write-Ahead Log) Writing** For any data modification:

**WAL Record Creation:**

- Backend creates WAL record describing the change
- WAL record contains:
    - Transaction ID
    - Operation type (INSERT/UPDATE/DELETE)
    - Before and after images (or delta)
    - Page location (relfilenode, block number, offset)

**WAL Buffer:**

- WAL record written to **WAL buffers** in shared memory
- WAL buffers are fixed-size circular buffers

**WAL Flush:** Triggered by:

- Transaction commit
- WAL buffer full
- Checkpoint
- `wal_writer` background process

**WAL Write Process:**

- WAL writer flushes WAL buffers to disk
- Writes to WAL segment files in `pg_wal/` directory
- Uses `fsync` to ensure durability on disk
- WAL files are sequentially written (fast)

**Durability Guarantee:**

- **WAL must be flushed BEFORE dirty data pages** (write-ahead protocol)
- Ensures crash recovery can replay changes
- Only after WAL flush is transaction considered durable

**Step 42: Buffer Dirty Marking**

- Modified pages marked as **dirty** in buffer pool
- Dirty pages eventually written by:
    - Background writer process
    - Checkpointer process
    - Backend process evicting page

---

#### Phase 5: Transaction Completion

##### 5.1 For Read-Only Queries (SELECT)

**Step 43: Implicit Commit**

- Query execution completes
- Implicit transaction automatically commits
- No WAL writes needed (no data changed)
- Snapshot released
- Locks released

##### 5.2 For Write Queries (INSERT/UPDATE/DELETE)

**Step 44: Commit Processing** User issues `COMMIT`:

**Step 45: Pre-Commit Checks**

- Validates no deferred constraint violations
- Checks triggers haven't aborted transaction

**Step 46: WAL Commit Record**

- Writes COMMIT record to WAL
- Flushes WAL to disk (fsync)
- **Transaction is now DURABLE**

**Step 47: CLOG Update**

- Updates CLOG to mark transaction as committed
- CLOG entry changed from "in progress" to "committed"

**Step 48: Cleanup**

- Releases transaction locks
- Clears transaction ID from active transaction list
- Transaction snapshots updated system-wide
- Other backends can now see committed changes

---

#### Phase 6: Result Return to Client

**Step 49: Result Formatting**

- Backend formats result rows into PostgreSQL wire protocol
- Each row encoded with column values
- Metadata sent: column names, types

**Step 50: Network Transmission**

- Results sent over TCP connection
- May be chunked if result set large
- Client receives rows incrementally

**Step 51: Query Completion**

- Backend sends `CommandComplete` message
- Includes row count affected/returned
- Sends `ReadyForQuery` status

**Step 52: Client Processing**

- Client library (libpq) receives result
- Parses wire protocol
- Presents data to application
- psql displays formatted table

---

#### Phase 7: Cleanup & Background Processes

###### 7.1 Autovacuum (Asynchronous)

**Step 53: Dead Tuple Tracking**

- UPDATE/DELETE operations leave dead tuples (old versions)
- `pg_stat_user_tables` tracks dead tuple count
- Autovacuum launcher monitors statistics

**Step 54: Vacuum Trigger** When threshold reached:

```
dead tuples > (autovacuum_vacuum_threshold + 
               autovacuum_vacuum_scale_factor * reltuples)
```

**Step 55: Vacuum Execution**

- Autovacuum worker starts vacuum on table
- Scans heap pages identifying dead tuples
- Marks dead tuple space as reusable
- Updates FSM (Free Space Map)
- Removes index entries for dead tuples
- Truncates empty pages at end of table (if possible)
- Prevents transaction ID wraparound

**Step 56: Analyze Execution**

- Updates statistics in `pg_statistic`
- Samples random rows from table
- Computes:
    - Most common values
    - Histograms
    - Null fractions
    - Correlation
- Better statistics → better query plans

###### 7.2 Checkpointing

**Step 57: Checkpoint Trigger** Triggered by:

- `checkpoint_timeout` elapsed
- WAL size exceeds `max_wal_size`
- Manual `CHECKPOINT` command
- Database shutdown

**Step 58: Checkpoint Process**

- Checkpointer process writes all dirty buffers to disk
- Creates checkpoint record in WAL
- Updates `pg_control` file with checkpoint location
- Allows old WAL files to be recycled
- Reduces crash recovery time

###### 7.3 Background Writer

**Step 59: Continuous Dirty Page Writing**

- Background writer periodically scans buffer pool
- Writes dirty pages to data files
- Reduces spike during checkpoint
- Keeps free buffers available

---

###### Complete Path Summary

Here's the entire flow:

1. **TCP connection** → 2. **Postmaster accepts** → 3. **Fork backend process** → 4. **pg_hba.conf check** → 5. **Authentication (password hash verify)** → 6. **Check pg_authid** → 7. **Verify LOGIN privilege** → 8. **Check CONNECT on database** → 9. **Session initialization** → 10. **Query received** → 11. **Lexical analysis** → 12. **Syntax parsing** → 13. **Parse tree creation** → 14. **Schema resolution** → 15. **USAGE privilege check** → 16. **SELECT privilege check** → 17. **Column validation** → 18. **Column-level permissions** → 19. **RLS policy application** → 20. **View expansion** → 21. **Query tree finalized** → 22. **Statistics lookup** → 23. **Access path generation** → 24. **Cost estimation** → 25. **Best plan selection** → 26. **Plan tree creation** → 27. **Executor initialization** → 28. **Transaction start** → 29. **Snapshot creation (MVCC)** → 30. **Buffer manager query** → 31. **Page fetch (disk/cache)** → 32. **Tuple access** → 33. **xmin/xmax check** → 34. **CLOG lookup** → 35. **Visibility determination** → 36. **WHERE evaluation** → 37. **Projection** → 38. **For writes: WAL record creation** → 39. **WAL buffer write** → 40. **WAL flush to disk** → 41. **Page dirty marking** → 42. **COMMIT processing** → 43. **CLOG update** → 44. **Lock release** → 45. **Result formatting** → 46. **Network send** → 47. **Client receives** → 48. **Autovacuum (background)** → 49. **Checkpoint (background)** → 50. **Connection remains open for next query**

This is the complete, detailed path from connection to query completion and beyond. Every permission check, every MVCC step, every WAL operation—it's all here.


od. This is **exactly** the right question, and you’re right to demand a **full path**.  
What you’re asking for is essentially:

> “From packet on the wire → to rows returned → to WAL on disk → to visibility rules”

I’ll give you the **end-to-end execution pipeline** of PostgreSQL.  
Not shortcuts. Not slogans. The _real path_.

Read this slowly. This is how DBAs and engine devs think.

---

###### 🧠 BIG PICTURE (keep this in your head)

A PostgreSQL query goes through **five major layers**:

1. **Connection & Authentication**
    
2. **Session & Authorization**
    
3. **Parsing → Rewriting → Planning**
    
4. **Execution (MVCC, buffers, joins, visibility)**
    
5. **WAL, commit, response**
    

Everything else fits _inside_ these.

---

###### 1️⃣ TCP CONNECTION & PROCESS CREATION

###### 1.1 Client initiates connection

Client (psql / app) opens a **TCP connection** to:

```
host:port (default 5432)
```

Postgres is already listening via:

- `postmaster` (aka `postgres`)
    
- Configured by `postgresql.conf`
    
- Controlled by `pg_hba.conf` for auth rules
    

---

###### 1.2 postmaster accepts connection

The **postmaster process**:

- Accepts the TCP socket
    
- Forks a **new backend process**
    
- That backend handles **exactly one client**
    

Important:

> PostgreSQL is **process-per-connection**, not threaded.

This backend:

- Has its own memory context
    
- Its own transaction state
    
- Its own locks
    

---

###### 2️⃣ AUTHENTICATION & ROLE RESOLUTION

##### 2.1 pg_hba.conf is checked

Before _anything_ else:

Postgres checks `pg_hba.conf` **top to bottom**:

- client IP
    
- database name
    
- role name
    
- auth method (md5, scram, peer, trust, etc.)
    

If no rule matches → connection rejected.

---

###### 2.2 Authentication happens

Example:

```text
user = bibek
password = bibek
```

Postgres:

- Hashes password
    
- Compares with `pg_authid`
    
- Verifies role existence
    

At this point:

- Role identity is **locked into session**
    
- Cannot change role unless `SET ROLE` allowed
    

---

###### 3️⃣ DATABASE & SESSION INITIALIZATION

###### 3.1 Database access check

Postgres verifies:

- Database exists
    
- Role has `CONNECT` privilege
    

Loads:

- `pg_database` entry
    
- Database OID
    
- Default tablespace
    

---

###### 3.2 Session state created

Backend initializes:

- Session memory context
    
- GUC parameters (work_mem, search_path, etc.)
    
- Transaction ID counter (but no transaction yet)
    

Now session is **idle**.

---

###### 4️⃣ QUERY ARRIVES (NOW THE REAL WORK STARTS)

Client sends query text:

```sql
SELECT ...
```

Backend begins **query pipeline**.

---

###### 5️⃣ PARSING (SYNTAX ONLY)

###### 5.1 Raw parsing

- SQL text → parse tree
    
- Grammar rules applied
    
- Syntax errors caught here
    

No tables.  
No permissions.  
No data.

Just structure.

---

###### 5.2 Query tree created

Internal representation:

- SELECT
    
- FROM
    
- JOIN
    
- WHERE
    
- GROUP BY
    

Still **names unresolved**.

---

###### 6️⃣ REWRITING (RULE SYSTEM)

###### 6.1 View expansion

If query references:

- VIEW
    
- RULE
    

Postgres:

- Rewrites query
    
- Replaces view with underlying SELECT
    
- Applies INSTEAD rules
    

This can **change the query shape dramatically**.

---

##### 7️⃣ SEMANTIC ANALYSIS (THIS IS CRITICAL)

Now Postgres starts _understanding meaning_.

###### 7.1 Name resolution

- Table names → OIDs
    
- Column names → attribute numbers
    
- Schema resolved via `search_path`
    

---

##### 7.2 Permission checks (multiple layers)

Postgres checks:

1. Role has access to **database**
    
2. Role has access to **schema**
    
3. Role has privileges on **table**
    
4. Role has privileges for **operation**
    
    - SELECT
        
    - INSERT
        
    - UPDATE
        
    - DELETE
        

If **any** fails → error.

Important:

> Permissions are checked **during parse/plan**, not execution.

---

###### 8️⃣ TRANSACTION BEGINS (IMPLICITLY)

Unless already in a transaction:

Postgres:

- Assigns a **transaction ID (XID)**
    
- Creates MVCC snapshot
    
- Snapshot includes:
    
    - xmin
        
    - xmax
        
    - list of active transactions
        

This snapshot defines **what data is visible**.

---

###### 9️⃣ PLANNING & OPTIMIZATION

##### 9.1 Logical plan

Planner:

- Determines join order
    
- Determines join type
    
- Applies predicate pushdown
    
- Estimates cardinality
    

Uses:

- Statistics (`pg_statistic`)
    
- Histograms
    
- Correlation data
    

---

###### 9.2 Physical plan selection

Planner chooses:

- Seq Scan / Index Scan / Bitmap Scan
    
- Nested Loop / Hash Join / Merge Join
    
- Sort / HashAggregate
    

Result:

> **Execution Plan Tree**

This is what `EXPLAIN` shows.

---

###### 🔥 KEY POINT

At this stage:

- No rows touched
    
- No WAL written
    
- No locks yet (beyond catalog access)
    

---

#### 🔟 EXECUTION PHASE (ROWS FINALLY MOVE)

Now executor runs the plan **node by node**.

---

###### 10.1 Buffer manager interaction

When a table/index page is needed:

1. Check **shared buffers**
    
2. If not found:
    
    - Read from disk
        
    - Load into buffer cache
        

No “load whole table” ever happens.

---

###### 10.2 MVCC visibility checks

For each row (tuple):

Postgres checks:

- `xmin` (creator XID)
    
- `xmax` (deleter XID)
    
- Snapshot rules
    

Row is:

- visible
    
- invisible
    
- dead
    

This happens **per row**.

---

###### 10.3 Join execution

Depending on plan:

- Nested loop:
    
    - Outer row
        
    - Inner lookup
        
- Hash join:
    
    - Build hash table
        
    - Probe
        
- Merge join:
    
    - Walk sorted streams
        

Each join node:

- Multiplies or discards rows
    

---

###### 10.4 Aggregation & sorting

If present:

- HashAggregate or GroupAggregate
    
- Sort nodes
    
- Memory → disk spill if needed
    

---

##### 11️⃣ WAL (Write-Ahead Logging)

###### For SELECT

- No WAL writes
    
- Purely read-only
    

##### For INSERT / UPDATE / DELETE

- WAL records generated
    
- Written **before** data pages flushed
    
- Ensures crash recovery
    

Important:

> WAL happens during execution, not at commit.

---

#### 12️⃣ COMMIT PHASE

If query is transactional and ends transaction:

1. Commit record written to WAL
    
2. Transaction marked committed
    
3. Snapshot invalidated
    
4. Locks released
    

Data pages:

- May still be dirty in memory
    
- WAL guarantees durability
    

---

##### 13️⃣ RESULT RETURN

Executor:

- Streams rows to client
    
- Uses protocol messages
    
- Does not buffer whole result unless needed
    

Client receives rows incrementally.

---

##### 14️⃣ SESSION CONTINUES OR ENDS

If connection stays open:

- Backend waits idle
    
- Holds no transaction
    
- Ready for next query
    

If connection closed:

- Backend exits
    
- Resources freed
    

###### 🧠 FINAL MENTAL MODEL (IMPORTANT)

A PostgreSQL query is **not**:

> connect → check → run → done

It is:

> connect  
> authenticate  
> authorize  
> parse  
> rewrite  
> resolve  
> plan  
> snapshot  
> execute (MVCC + buffers + joins)  
> WAL  
> commit  
> stream result

---

###### Why this matters (interview & real life)

If you understand this path:

- You know **where errors occur**
    
- You know **why EXPLAIN doesn’t show execution**
    
- You know **why permissions fail early**
    
- You know **why MVCC affects performance**
    
- You know **why indexes don’t always help**
    

This is **not junior knowledge**. This is **core engine literacy**.

---

###### Next (VERY IMPORTANT)

If you want, next we can:

- Walk **one query through this path line-by-line**
    
- Map each stage to `ps`, `lsof`, `strace`
    
- Correlate this with `EXPLAIN ANALYZE`
    
- Or connect this to **deadlocks & locks**
    

Just say **which layer** you want to zoom into next.