# JDBC Mastery Guide (Before Spring / Spring Data)

Goal: understand JDBC deeply enough that Spring's `JdbcTemplate`, connection pools, and transaction management never feel like magic.

---

## 1. Why JDBC Exists (the mental model)

Every database vendor (MySQL, PostgreSQL, Oracle, etc.) has its own wire protocol and internal APIs. Without a standard, every Java app would need vendor-specific code to talk to each database.

**JDBC (Java Database Connectivity)** is a standard API (a set of interfaces in `java.sql` / `javax.sql`) that lets Java code talk to _any_ relational database the same way. The actual database-specific work is done by a **driver** — a jar supplied by the vendor that implements the JDBC interfaces.

Think of JDBC as **an interface contract**:

- `java.sql.Driver`, `Connection`, `Statement`, `ResultSet` → interfaces JDBC defines
- `mysql-connector-j`, `postgresql` jar → vendor's concrete implementation of those interfaces

This is precisely the pattern Spring later abstracts further with `JdbcTemplate`, `DataSource`, and ORMs like Hibernate — they all sit on top of raw JDBC.

---

## 2. The Core JDBC Interfaces (know these cold)

|Interface|Role|
|---|---|
|`DriverManager`|Legacy way to obtain a `Connection` using a JDBC URL|
|`Driver`|Vendor's implementation that knows how to talk to a specific DB|
|`DataSource`|Modern, preferred way to obtain a `Connection` (supports pooling, JNDI)|
|`Connection`|A live session/socket to the database|
|`Statement`|Executes a **static** SQL query (no parameters)|
|`PreparedStatement`|Executes a **precompiled**, **parameterized** SQL query|
|`CallableStatement`|Executes a stored procedure|
|`ResultSet`|The tabular result of a query, with a cursor you iterate over|
|`ResultSetMetaData`|Info about the shape of a `ResultSet` (column names, types, count)|
|`DatabaseMetaData`|Info about the database itself (vendor, version, supported features)|
|`SQLException`|Checked exception every JDBC operation can throw|

---

## 3. The Classic JDBC Workflow (the 6 steps, memorize this)

```java
// 1. Load driver (NOT needed since JDBC 4.0 — auto-discovered via ServiceLoader)
Class.forName("com.mysql.cj.jdbc.Driver"); // legacy, rarely needed today

// 2. Establish connection
Connection conn = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/mydb", "user", "password");

// 3. Create statement
PreparedStatement ps = conn.prepareStatement(
    "SELECT id, name FROM users WHERE age > ?");

// 4. Set parameters & execute
ps.setInt(1, 18);
ResultSet rs = ps.executeQuery();

// 5. Process results
while (rs.next()) {
    int id = rs.getInt("id");
    String name = rs.getString("name");
}

// 6. Close resources (in reverse order: ResultSet -> Statement -> Connection)
rs.close();
ps.close();
conn.close();
```

**Interview Q: Why isn't `Class.forName()` needed anymore?** → Since JDBC 4.0 (Java 6+), drivers are auto-loaded via the `java.util.ServiceLoader` mechanism, using a file `META-INF/services/java.sql.Driver` inside the driver jar. `DriverManager` discovers it automatically at classpath scan time.

---

## 4. JDBC URL Anatomy

```
jdbc:<subprotocol>://<host>:<port>/<database>?param1=value1&param2=value2
```

Examples:

```
jdbc:mysql://localhost:3306/mydb?useSSL=false&serverTimezone=UTC
jdbc:postgresql://localhost:5432/mydb
jdbc:oracle:thin:@localhost:1521:orcl
jdbc:h2:mem:testdb   (in-memory H2, extremely common for tests)
```

---

## 5. Statement vs PreparedStatement vs CallableStatement (heavily interviewed)

||Statement|PreparedStatement|CallableStatement|
|---|---|---|---|
|SQL compiled|every execution|once, reused|once, reused|
|Parameters|string concatenation (unsafe)|`?` placeholders, type-safe setters|`?` placeholders + IN/OUT params|
|SQL Injection risk|**high**|**protected**|**protected**|
|Performance (repeated calls)|poor|good — DB can cache execution plan|good|
|Use case|one-off static/DDL queries|any query with parameters (basically always)|calling stored procedures|

```java
// Statement — DANGEROUS, never do this with user input
Statement st = conn.createStatement();
ResultSet rs = st.executeQuery("SELECT * FROM users WHERE name = '" + userInput + "'");
// vulnerable to: userInput = "x' OR '1'='1"

// PreparedStatement — SAFE
PreparedStatement ps = conn.prepareStatement("SELECT * FROM users WHERE name = ?");
ps.setString(1, userInput);
```

**Interview Q: Why does PreparedStatement prevent SQL injection?** → The SQL structure is sent to the DB and compiled _before_ parameter values are bound. Parameter values are always treated as **data**, never as executable SQL syntax — so injected SQL syntax in a parameter is just treated as a literal string value.

**Interview Q: Difference between `execute()`, `executeQuery()`, and `executeUpdate()`?**

- `executeQuery()` → for `SELECT`, returns a `ResultSet`
- `executeUpdate()` → for `INSERT`/`UPDATE`/`DELETE`/DDL, returns an `int` (rows affected, or 0 for DDL)
- `execute()` → generic; returns `boolean` (true if result is a `ResultSet`); used when you don't know ahead of time what kind of statement it is, or it returns multiple results

---

## 6. CallableStatement (Stored Procedures)

```java
CallableStatement cs = conn.prepareCall("{call getUserById(?, ?)}");
cs.setInt(1, userId);                       // IN parameter
cs.registerOutParameter(2, Types.VARCHAR);  // OUT parameter
cs.execute();
String name = cs.getString(2);
```

- `IN` — passed into the procedure
- `OUT` — returned from the procedure
- `INOUT` — both

---

## 7. ResultSet Deep Dive

### Cursor Types (set when creating the Statement)

```java
Statement st = conn.createStatement(
    ResultSet.TYPE_SCROLL_INSENSITIVE,   // scrollability
    ResultSet.CONCUR_READ_ONLY);         // concurrency
```

|Type constant|Meaning|
|---|---|
|`TYPE_FORWARD_ONLY` (default)|cursor moves forward only, once — cheapest|
|`TYPE_SCROLL_INSENSITIVE`|can move forward/backward, but a snapshot — doesn't see DB changes made after query ran|
|`TYPE_SCROLL_SENSITIVE`|can move forward/backward, and reflects live DB changes|

|Concurrency constant|Meaning|
|---|---|
|`CONCUR_READ_ONLY` (default)|cannot update rows via ResultSet|
|`CONCUR_UPDATABLE`|can call `rs.updateX()` + `rs.updateRow()` to modify data directly through the ResultSet|

**Quirk:** Forward-only `ResultSet` throws `SQLException` if you call `rs.previous()` or `rs.first()` — a very common beginner runtime bug.

### Navigating

```java
rs.next();      // move forward, returns false when no more rows
rs.previous();  // only if scrollable
rs.first(); rs.last(); rs.absolute(5); rs.relative(-2);
rs.beforeFirst(); rs.afterLast();
```

### Getting values — by index vs by column name

```java
rs.getInt(1);          // 1-based index (NOT 0-based — classic gotcha!)
rs.getInt("age");      // by column label — slightly slower but more readable/robust
```

**Quirk:** JDBC indices are **1-based**, unlike almost everything else in Java. This bites everyone at least once.

### NULL handling gotcha

```java
int age = rs.getInt("age"); // if DB value is NULL, this silently returns 0!
if (rs.wasNull()) {
    // the previous getX() call actually returned a DB NULL, not a real 0
}
```

**Interview Q: How do you correctly detect a NULL column value from a ResultSet for a primitive getter?** → Call `rs.wasNull()` immediately after the getter — it tells you whether the last-read value was actually SQL NULL (returned as 0/false/null by the getter) or a genuine primitive value.

---

## 8. Connection Management

### DriverManager vs DataSource

```java
// DriverManager — simple, but creates a NEW physical connection every call. No pooling.
Connection c = DriverManager.getConnection(url, user, pass);

// DataSource — the modern/production way. Usually backed by a connection pool.
DataSource ds = ...; // e.g., HikariCP, obtained via JNDI or configured bean
Connection c = ds.getConnection(); // usually returns a pooled, reused connection
```

**Interview Q: Why prefer DataSource over DriverManager in real applications?** → `DataSource` decouples connection configuration from code (can be configured externally/via JNDI), and — critically — production `DataSource` implementations wrap a **connection pool**, avoiding the expensive cost of opening/closing a TCP+auth handshake on every single request. `DriverManager` gives you a brand-new raw connection every time.

### Why Connection Pooling Matters

Opening a DB connection involves: TCP handshake, authentication, session setup on the DB server — this can take tens to hundreds of milliseconds. Doing this per-request kills throughput. A pool keeps a set of already-open connections ready to hand out and reuse.

Common pool implementations: **HikariCP** (Spring Boot's default since 2.x), Apache DBCP2, C3P0, Tomcat JDBC Pool.

**Key pool config knobs (interview-relevant, map directly to Spring Boot properties):**

- `maximumPoolSize` — max connections held
- `minimumIdle` — min idle connections kept ready
- `connectionTimeout` — how long a thread waits for a free connection before failing
- `idleTimeout` — how long an idle connection can sit before being closed
- `maxLifetime` — max connection age before it's retired (avoids stale/half-dead connections)
- `leakDetectionThreshold` — warns if a connection is checked out too long without being returned (helps catch "forgot to close" bugs)

**Quirk / classic production bug: Connection Leaks.** If you forget to `close()` a connection (e.g., an exception is thrown before you reach `conn.close()`), that connection never returns to the pool. Enough leaks and the pool exhausts, and every new request hangs/times out waiting for a connection. This is why **try-with-resources** matters so much (see below), and why Spring's `JdbcTemplate` handles closing for you automatically.

---

## 9. Resource Management: try-with-resources

`Connection`, `Statement`, `PreparedStatement`, and `ResultSet` all implement `AutoCloseable`.

```java
try (Connection conn = dataSource.getConnection();
     PreparedStatement ps = conn.prepareStatement(sql);
     ResultSet rs = ps.executeQuery()) {

    while (rs.next()) {
        // process
    }
} catch (SQLException e) {
    // handle
}
// all three auto-closed here, in REVERSE order of declaration, even if an exception was thrown
```

**Interview Q: Why is try-with-resources important in JDBC specifically?** → Because JDBC resources hold onto scarce OS/network resources (sockets, DB-side session slots). If not closed — especially when an exception occurs mid-operation — connections leak out of the pool, eventually exhausting it and hanging the whole application. Try-with-resources guarantees `close()` is called even on exception paths.

---

## 10. Transactions (critical for interviews — this is where Spring's `@Transactional` comes from)

By default, JDBC connections are in **auto-commit mode**: every single statement is its own transaction, committed immediately after execution.

### Manual transaction control

```java
conn.setAutoCommit(false);
try {
    ps1.executeUpdate(); // debit account A
    ps2.executeUpdate(); // credit account B
    conn.commit();        // both succeed together
} catch (SQLException e) {
    conn.rollback();      // undo both if either fails
} finally {
    conn.setAutoCommit(true); // restore default before returning connection to pool!
}
```

**Interview Q: What's the classic bank-transfer example used to explain JDBC transactions?** → Transferring money involves two updates (debit one account, credit another). If the first succeeds but the second fails and there's no transaction wrapping both, you get an inconsistent state (money vanishes). Turning off auto-commit and wrapping both statements in a single `commit()`/`rollback()` ensures atomicity.

### ACID (know these definitions cold)

- **Atomicity** — all operations in a transaction succeed, or none do
- **Consistency** — a transaction moves the DB from one valid state to another, respecting constraints
- **Isolation** — concurrent transactions don't interfere with each other's intermediate state
- **Durability** — once committed, changes survive even a crash

### Transaction Isolation Levels (very commonly asked)

```java
conn.setTransactionIsolation(Connection.TRANSACTION_READ_COMMITTED);
```

|Level|Dirty Read|Non-repeatable Read|Phantom Read|
|---|---|---|---|
|`TRANSACTION_READ_UNCOMMITTED`|possible|possible|possible|
|`TRANSACTION_READ_COMMITTED` (most DBs' default)|prevented|possible|possible|
|`TRANSACTION_REPEATABLE_READ`|prevented|prevented|possible|
|`TRANSACTION_SERIALIZABLE`|prevented|prevented|prevented|

- **Dirty read** — reading uncommitted changes from another transaction (which might get rolled back)
- **Non-repeatable read** — re-reading the same row twice in a transaction gives different values because another transaction committed an update in between
- **Phantom read** — re-running the same range query twice gives a different _set of rows_ because another transaction inserted/deleted rows matching the criteria in between

**Interview Q: Trade-off of higher isolation levels?** → Higher isolation (e.g., `SERIALIZABLE`) gives stronger consistency guarantees but costs more performance/concurrency (more locking, more chance of contention/deadlocks). `READ_COMMITTED` is the common default balance in most production systems.

### Savepoints

```java
Savepoint sp = conn.setSavepoint("beforeRiskyOp");
try {
    riskyOperation();
} catch (SQLException e) {
    conn.rollback(sp); // roll back only to the savepoint, not the whole transaction
}
```

---

## 11. Batch Processing (performance topic, commonly asked)

Executing many similar statements one at a time means one network round-trip per statement. Batching sends them together.

```java
conn.setAutoCommit(false);
PreparedStatement ps = conn.prepareStatement("INSERT INTO users(name) VALUES (?)");
for (String name : names) {
    ps.setString(1, name);
    ps.addBatch();
}
int[] results = ps.executeBatch(); // single round-trip (or fewer round-trips)
conn.commit();
```

**Interview Q: Why use batching, and why combine it with `setAutoCommit(false)`?** → Batching drastically reduces network round-trips for bulk inserts/updates, which is often the biggest performance win available. Turning off auto-commit avoids committing after every single statement in the batch (which would defeat the purpose and also be slow); one commit at the end persists the whole batch atomically.

---

## 12. Metadata APIs

```java
// About the ResultSet's shape
ResultSetMetaData rsmd = rs.getMetaData();
int columnCount = rsmd.getColumnCount();
String colName = rsmd.getColumnName(1);
int colType = rsmd.getColumnType(1);

// About the database itself
DatabaseMetaData dbmd = conn.getMetaData();
String dbProductName = dbmd.getDatabaseProductName();
String driverVersion = dbmd.getDriverVersion();
ResultSet tables = dbmd.getTables(null, null, "%", new String[]{"TABLE"});
```

Used for generic/dynamic tooling — e.g., building a generic CRUD framework or a database explorer without knowing the schema ahead of time.

---

## 13. Exception Handling: SQLException Chain

```java
try {
    // jdbc code
} catch (SQLException e) {
    while (e != null) {
        System.out.println("SQLState: " + e.getSQLState());
        System.out.println("ErrorCode: " + e.getErrorCode());
        System.out.println("Message: " + e.getMessage());
        e = e.getNextException(); // SQLExceptions can be chained!
    }
}
```

- `getSQLState()` — standardized (X/Open / ANSI) error code, portable across vendors
- `getErrorCode()` — vendor-specific numeric error code
- `getNextException()` — SQLException is a **linked chain**, not a single exception — a batch operation, for instance, can report multiple underlying failures chained together

**Interview Q: Why does Spring wrap SQLException into its own `DataAccessException` hierarchy?** → `SQLException` is a checked exception, forcing every DAO method up the call stack to declare/catch it — awkward and vendor-specific (error codes and messages vary by DB). Spring translates vendor-specific `SQLException`s into a consistent, unchecked `DataAccessException` hierarchy (`DuplicateKeyException`, `DataIntegrityViolationException`, etc.), decoupling calling code from any specific driver's exception format.

---

## 14. RowSet (brief, good to know exists)

`RowSet` (`javax.sql.RowSet`) extends `ResultSet` and can be **disconnected** — i.e., you can read data, close the connection, and still work with the results in memory. Useful for passing data around without holding a live DB connection open. `CachedRowSet` is the common disconnected implementation. Rare in modern Spring apps (Spring's own abstractions replace this need) but sometimes asked about conceptually.

---

## 15. JDBC Driver Types (rarely used today, but classic interview trivia)

|Type|Description|
|---|---|
|Type 1|JDBC-ODBC bridge — translates JDBC calls to ODBC (removed in Java 8, obsolete)|
|Type 2|Native-API driver — partly Java, partly native vendor client libraries|
|Type 3|Network-protocol driver — pure Java, talks to a middleware server which talks to the DB|
|Type 4|Pure Java, thin driver — pure Java, talks directly to DB using its native network protocol|

**Interview Q: Which driver type is most commonly used today, and why?** → **Type 4** — pure Java, no native libraries or middleware needed, platform-independent, directly implements the DB's wire protocol. `mysql-connector-j` and PostgreSQL's driver are both Type 4.

---

## 16. How This Maps Directly to Spring (the payoff)

Once you know raw JDBC, Spring's abstractions become obviously just "the boilerplate above, automated":

|Raw JDBC concept|Spring equivalent|
|---|---|
|`DataSource` + manual pooling setup|Spring Boot auto-configures a `DataSource` bean (HikariCP by default) from `application.properties`|
|Manual `try (Connection...) {}` boilerplate|`JdbcTemplate` opens/closes connections and statements for you automatically|
|Manual `ResultSet` row-to-object mapping|`RowMapper<T>` interface — you just write the mapping logic, Spring handles iteration|
|`conn.setAutoCommit(false)` + `commit()`/`rollback()`|`@Transactional` annotation — Spring manages the transaction boundary via AOP proxy|
|`SQLException` chains, vendor-specific codes|`DataAccessException` hierarchy — unchecked, vendor-agnostic|
|Manual batch loop with `addBatch()`|`JdbcTemplate.batchUpdate()`|
|Manual `PreparedStatement` parameter setting|`JdbcTemplate.update(sql, param1, param2...)` with varargs|

---

## 17. Essential Interview Question Bank (rapid fire)

1. What is JDBC? → a standard Java API for vendor-neutral relational database access, implemented per-vendor by drivers.
2. What are the main JDBC interfaces? → `Driver`, `DriverManager`, `DataSource`, `Connection`, `Statement`/`PreparedStatement`/`CallableStatement`, `ResultSet`.
3. Difference between `Statement` and `PreparedStatement`? → precompilation, parameterization, SQL-injection safety, performance on repeated execution.
4. Why is `PreparedStatement` safer against SQL injection? → parameter values are bound as data after the SQL structure is already compiled, never interpreted as SQL syntax.
5. Difference between `executeQuery`, `executeUpdate`, `execute`? → return types and intended statement kind (SELECT vs DML/DDL vs unknown).
6. What is a `ResultSet` cursor, and what are the scrollability/concurrency types? → `TYPE_FORWARD_ONLY`/`SCROLL_INSENSITIVE`/`SCROLL_SENSITIVE`; `CONCUR_READ_ONLY`/`CONCUR_UPDATABLE`.
7. Why are ResultSet column indices 1-based instead of 0-based? → historical convention matching SQL's own 1-based column ordering; just something to memorize.
8. How do you detect a SQL NULL when reading a primitive column? → `rs.wasNull()` after the getter call.
9. What's the difference between `DriverManager` and `DataSource`? → `DriverManager` opens a brand-new raw connection each call; `DataSource` is the standard abstraction typically backed by a connection pool, and supports JNDI lookup.
10. Why use connection pooling? → connection setup (TCP + auth + session init) is expensive; pooling reuses already-open connections for far better throughput.
11. What causes a connection pool to become exhausted, and how do you prevent it? → connection leaks (not closing connections, e.g., on exception paths); prevent via try-with-resources and pool leak-detection settings.
12. What is auto-commit mode, and why turn it off? → default mode where every statement is its own transaction; turned off to group multiple statements into one atomic transaction.
13. Explain ACID. → Atomicity, Consistency, Isolation, Durability — the four guarantees of a reliable transaction.
14. What are the transaction isolation levels, and what anomaly does each prevent? → READ_UNCOMMITTED (none) → READ_COMMITTED (prevents dirty reads) → REPEATABLE_READ (prevents non-repeatable reads too) → SERIALIZABLE (prevents phantom reads too).
15. Define dirty read, non-repeatable read, phantom read. → (see definitions in section 10)
16. What is a savepoint used for? → partial rollback within a larger transaction, without discarding the whole transaction.
17. Why and how do you use JDBC batch processing? → reduces network round-trips for bulk DML via `addBatch()`/`executeBatch()`, typically combined with manual commit control.
18. What is `SQLState` vs vendor error code in `SQLException`? → SQLState is a standardized cross-vendor code; the vendor error code is DB-specific and non-portable.
19. Why does `SQLException` support exception chaining (`getNextException`)? → certain operations (e.g., batch updates) can produce multiple independent underlying failures that all need to be reported, not just the first one.
20. What are the 4 JDBC driver types, and which is most common today? → Type 1 (JDBC-ODBC bridge, obsolete), Type 2 (native-API), Type 3 (network-protocol/middleware), Type 4 (pure Java thin driver) — Type 4 is standard today.
21. Why does Spring wrap `SQLException` into `DataAccessException`? → to provide a consistent, unchecked, vendor-independent exception hierarchy instead of forcing checked-exception handling of vendor-specific codes everywhere.
22. What does `JdbcTemplate` actually save you from writing? → the entire connection/statement/resultset open-close boilerplate, exception translation, and (with `RowMapper`) the manual row-to-object mapping loop.

---

## 18. Suggested Order to Actually Learn This

1. Set up an **H2 in-memory database** (`jdbc:h2:mem:testdb`) — zero external setup needed, perfect for practicing.
2. Write raw JDBC code (no Spring) to: connect, create a table, insert rows with `PreparedStatement`, and query them back with a `while (rs.next())` loop.
3. Intentionally trigger a `NullPointerException`/wrong-value bug by ignoring `wasNull()`, then fix it — internalize the NULL gotcha.
4. Write a manual transaction: two updates, `setAutoCommit(false)`, commit/rollback in try/catch — simulate a failure and confirm rollback actually undoes both.
5. Try each isolation level conceptually (reading about them is fine; reproducing race conditions live is advanced/optional).
6. Write a batch insert of 1000 rows with and without `addBatch()`/`executeBatch()`, and time the difference — you'll _feel_ why batching matters.
7. Deliberately forget to close a `Connection` in a loop against a small pool (e.g., HikariCP with `maximumPoolSize=2`) and watch it hang — this is the single most valuable "aha" exercise for understanding pool exhaustion.
8. Only THEN move to `JdbcTemplate` — you'll immediately see it's just steps 2–7, automated.

Once this is solid, Spring's `@Transactional`, `JdbcTemplate`, `RowMapper`, and `DataAccessException` will all map cleanly onto concepts you already understand from the inside out — instead of feeling like unexplained annotations.


```
Java Application
       │
       ▼
    Hibernate
       │
       ▼
      JDBC
       │
       ▼
 JDBC Driver
       │
       ▼
   Database
```

```
Maven
  ↓
Basic JDBC          ← learn this
  ↓
Hibernate           ← then learn this
  ↓
JPA
  ↓
Spring Data JPA
```

why need prepared statement