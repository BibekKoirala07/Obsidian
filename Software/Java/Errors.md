# Learning Maven & JDBC Through Their Errors

Philosophy: an error message is a window into how the tool actually works internally. If you understand _why_ an error happens mechanically, you understand the underlying system — not just the fix.

---

# PART 1: MAVEN ERRORS

## 1. Dependency Resolution Errors

### `Could not resolve dependencies for project ... : Failure to find X:Y:jar:Z in ...`

**What's actually happening:** Maven walked its repository search order (local `~/.m2` → configured remotes → Central) and found the artifact nowhere. This is a pure "the jar doesn't exist where Maven looked" error.

**Common root causes:**

- Typo in `groupId`/`artifactId`/`version` (GAV mismatch — teaches you GAV coordinates must be _exact_)
- Version doesn't exist yet (e.g., you typed a future/unreleased version)
- Artifact is in a private/internal repo you haven't declared in `<repositories>`
- Corporate proxy/firewall blocking Maven Central

**What it teaches:** Maven doesn't "know" about every jar in existence — it only looks where you've told it to look, in a defined order. This is the whole repository-resolution model made visible.

---

### `Failed to read artifact descriptor for X:Y:jar:Z`

**What's happening:** Maven found _references_ to the artifact but couldn't fetch its POM (metadata file) — often because the POM download failed, not the jar itself.

**Teaches:** every artifact download is actually (at least) two files — the `.pom` and the `.jar` — and both must succeed.

---

### `ResolutionException` / stale `.lastUpdated` files

**What's happening:** A previous failed/interrupted download left a marker file (`artifactId-version.jar.lastUpdated`) in your local repo. Maven sees this marker and refuses to re-attempt the download for a while (to avoid hammering a broken repo), even though the network issue is now fixed.

**Fix:** delete the specific artifact's folder under `~/.m2/repository/...` and rebuild.

**Teaches:** the local repo isn't just a cache of successes — it also caches _failures_, which is why "just fix your internet and retry" sometimes doesn't work until you manually clear the stale marker.

---

### `Unable to find valid certification path to requested target` (SSL errors during download)

**What's happening:** Maven is fetching over HTTPS and the JVM's trust store doesn't trust the repo server's SSL certificate (common behind corporate proxies doing SSL inspection, or with self-hosted Nexus using self-signed certs).

**Teaches:** Maven's HTTP client obeys the same JVM-level SSL trust rules as any other Java networking code — it's not magic, it's just an HTTP client under the hood.

---

## 2. Dependency Conflict / Version Errors

### `NoSuchMethodError` / `NoClassDefFoundError` at _runtime_, even though the build succeeded

**What's happening:** This is NOT a Maven error technically — Maven compiled fine. But at runtime, the JVM classloader picked up a _different version_ of a class than the one your code was compiled against. Classic cause: **dependency mediation** picked an unexpected transitive version (the "nearest wins" rule), or two dependencies pulled in incompatible versions of the same library and only one made it onto the classpath.

**Diagnosis command:** `mvn dependency:tree` — shows exactly which version won and why.

**Teaches:** compile-time and runtime classpaths aren't guaranteed to be self-consistent just because the build succeeded. This is one of the most common real bugs in Spring projects (e.g., two starters pulling different Jackson versions).

---

### `DependencyConvergence` failures (when the enforcer plugin is used)

**What's happening:** The `maven-enforcer-plugin`'s `dependencyConvergence` rule fails the build on purpose when transitive dependencies bring in conflicting versions of the same artifact — turning a _silent_ runtime risk into a _loud_ build-time failure.

**Teaches:** version conflicts always exist as a possibility in any non-trivial dependency tree; the enforcer plugin exists specifically because "nearest wins" mediation is a footgun that teams want to catch early.

---

## 3. Build Lifecycle / Plugin Errors

### `No goals have been specified for this build. You must specify a valid lifecycle phase...`

**What's happening:** You ran plain `mvn` with no phase or goal argument. Maven's lifecycle framework requires an explicit target (`compile`, `install`, `plugin:goal`, etc.) — it has no meaningful default.

**Teaches:** there is no such thing as "just run Maven" — every invocation must name a phase or goal, reinforcing that the lifecycle is a sequence you explicitly enter at some point.

---

### `Plugin X:Y:Z or one of its dependencies could not be resolved`

**What's happening:** Same resolution mechanism as dependency errors (Part 1), but for a _plugin_ rather than a library dependency. Plugins are themselves just artifacts fetched from repos.

**Teaches:** plugins aren't built into Maven core — the compiler plugin, surefire plugin, etc. are all downloaded artifacts too, which is why a fresh machine with no internet can fail even on `mvn compile`.

---

### `Compilation failure` / `[ERROR] .../MyClass.java:[12,5] cannot find symbol`

**What's happening:** This is the `maven-compiler-plugin`'s `compile` goal failing — a plain javac error, just reported through Maven's output formatting.

**Teaches:** Maven doesn't have its own compiler — it's invoking `javac` (via the compiler plugin) under the hood. Maven errors during `compile` phase are really just Java compiler errors with a Maven wrapper around them.

---

### `There are test failures` / Surefire test report errors

**What's happening:** The `maven-surefire-plugin`, bound to the `test` phase, ran your unit tests and one or more `assert`/exception occurred. By default, this **fails the entire build** — later phases (`package`, `install`) never execute.

**Fix (understand, don't just skip blindly):** `-DskipTests` (compiles tests, skips running) vs `-Dmaven.test.skip=true` (skips compiling AND running).

**Teaches:** the "run every phase up to and including the target phase" rule is not cosmetic — a failure in an early phase (`test`) genuinely blocks all later phases (`package`, `install`, `deploy`). This is _why_ CI pipelines fail entire builds over a single broken unit test.

---

### `Unable to locate the Javac Compiler in: ...` / build fails only in CI, not locally

**What's happening:** Maven is running under a JRE, not a JDK (no compiler available), often because `JAVA_HOME` in the CI environment points to a JRE-only install.

**Teaches:** Maven itself is just a Java program — it needs the _same_ JDK availability as any other Java compilation task; it's not bundling its own compiler.

---

### `Failed to execute goal ... spring-boot-maven-plugin ... repackage`

**What's happening:** Usually because there's no valid main class detected, or the artifact was already repackaged, or the packaging type doesn't match (e.g., trying to repackage a `pom`-packaged module).

**Teaches:** the "fat jar" Spring Boot produces isn't something `package` phase does alone — `repackage` is a _second_, separate goal from `spring-boot-maven-plugin` that runs after the normal jar is built, transforming a normal thin jar into an executable one. If your plugin config is wrong, you silently get a _thin_, non-runnable jar instead, and only discover it when `java -jar app.jar` fails with `no main manifest attribute`.

---

## 4. POM Structure / Syntax Errors

### `Non-parseable POM ... : ... must be terminated by the matching end-tag`

**What's happening:** Literally malformed XML — Maven's POM parser is just an XML parser first, before any Maven-specific logic runs at all.

**Teaches:** pom.xml has zero tolerance for XML errors; Maven can't even begin resolving lifecycle/dependencies until the file parses as valid XML.

---

### `'dependencies.dependency.version' for X:Y:jar is missing`

**What's happening:** You declared a dependency with no version, and no `dependencyManagement`/parent/BOM provides one for that GA (groupId:artifactId) either.

**Teaches:** "version-less dependencies" in Spring Boot projects aren't a Maven default behavior — they only work because something _upstream_ (a BOM or parent) is silently supplying the version via `dependencyManagement`. If that chain breaks (e.g., you remove the parent), this exact error reappears, revealing the mechanism.

---

### `Child module ... does not exist` (multi-module projects)

**What's happening:** the parent's `<modules>` list references a directory that doesn't exist or is misspelled.

**Teaches:** `<modules>` entries are just relative directory paths — there's no separate module registry; Maven literally looks for a `pom.xml` inside that folder path.

---

### `'parent.relativePath' ... points at wrong local POM`

**What's happening:** the child pom's `<parent>` block (with an explicit or default `<relativePath>`) doesn't find a parent pom.xml at that filesystem location — it tries `../pom.xml` by default.

**Teaches:** parent resolution first tries a **relative filesystem path**, and only falls back to repository lookup (local/remote) if that fails — this is why moving a multi-module project's folder structure around can suddenly break builds that "used to work."

---

### Duplicate declaration ("some problems were encountered while building the effective model" + duplicate dependency warnings)

**What's happening:** You declared the same `groupId:artifactId` twice in `<dependencies>` (often once directly, once via copy-paste error). Maven doesn't error hard on this by default but warns, and behavior can be inconsistent about which one applies.

**Teaches:** run `mvn help:effective-pom` whenever behavior seems inconsistent with what you wrote — the "effective" merged config is the real source of truth, not your raw file.

---

## 5. Environment / Local Setup Errors

### `mvn: command not found`

**What's happening:** Maven binary isn't on `PATH`. Pure OS/shell issue, nothing Maven-specific.

**Teaches:** distinguishes "Maven itself has a problem" errors from "your shell can't even find Maven" — always the first thing to rule out.

---

### `JAVA_HOME is not defined correctly / not found`

**What's happening:** Maven needs a JDK to run and delegates to whatever `JAVA_HOME` points to; if unset or pointing to a non-existent path, Maven can't even boot the JVM tasks it needs internally.

**Teaches:** Maven is not JVM-agnostic tooling — it runs _inside_ a JVM you must correctly point it to, exactly like any other Java process.

---

### Builds work locally but fail on CI/another machine ("works on my machine")

**Root cause almost always one of:**

- Different local `~/.m2` cache masking a missing-dependency problem locally
- Different JDK version (`maven.compiler.source/target` mismatch)
- Missing Maven Wrapper (`mvnw`) usage — CI used a different global Maven version

**Teaches:** exactly why Spring Boot projects ship the Maven Wrapper — to eliminate "different Maven version" as a variable across machines.

---

# PART 2: JDBC ERRORS

## 6. Connection Errors

### `java.sql.SQLException: No suitable driver found for jdbc:...`

**What's happening:** `DriverManager` couldn't find any registered `Driver` implementation that claims to understand your JDBC URL's subprotocol (e.g., `jdbc:mysql:`, `jdbc:postgresql:`). This means either the driver jar isn't on the classpath at all, or its `META-INF/services/java.sql.Driver` service file didn't get picked up.

**Teaches:** JDBC's driver auto-registration (`ServiceLoader` mechanism, section 3 of the JDBC guide) is real infrastructure that can actually fail — it's not guaranteed magic; the driver jar genuinely has to be present and correctly packaged.

---

### `Communications link failure` (MySQL) / `Connection refused` (general)

**What's happening:** The JDBC URL is syntactically fine and the driver loaded, but the actual TCP connection to `host:port` failed — DB not running, wrong port, firewall blocking, or wrong host.

**Teaches:** JDBC's `Connection` really is a live network socket underneath — this error occurs at the TCP layer, entirely below JDBC's own logic, proving `Connection` is not some lazy abstract handle but an actual open connection.

---

### `Access denied for user 'x'@'host' (using password: YES)`

**What's happening:** Authentication rejected — wrong username/password, or the DB user lacks permission to connect from your host (common with MySQL's host-based grants).

**Teaches:** authentication happens as part of establishing the `Connection` itself (step 2 of the classic 6-step workflow), before any SQL is even sent — so this error occurs before you ever get to write a query.

---

### `The server time zone value '...' is unrecognized...` (MySQL driver quirk)

**What's happening:** Newer MySQL Connector/J versions require an explicit timezone parameter (`serverTimezone=UTC`) in the JDBC URL because it refuses to guess.

**Teaches:** JDBC URL query parameters aren't just cosmetic — they configure real driver behavior, and some parameters became _mandatory_ between driver versions (a nice lesson in how driver upgrades can silently break existing code).

---

### `SQLException: Connection is closed` (thrown from any statement/resultset call)

**What's happening:** You tried to use a `Connection`, `Statement`, or `ResultSet` after it (or its parent `Connection`) was already closed — often because it was returned to a pool (or closed by pool timeout) and you kept a stale reference.

**Teaches:** in pooled scenarios, "closing" a `Connection` from `DataSource.getConnection()` doesn't actually close the physical socket — it returns it to the pool. But logically-closed handles you're still holding onto (e.g., stored in a field beyond the pool's lease) become dead references. This reveals the difference between the _logical_ `Connection` object handed to you and the _physical_ connection the pool manages underneath.

---

### `Connection is not available, request timed out after Xms` (HikariCP)

**What's happening:** The connection pool has zero free connections and your thread waited past `connectionTimeout` for one to free up. Not a network problem — a **saturation** problem.

**Root causes:**

- Connection leaks (see JDBC guide section 8) — connections checked out and never returned
- Genuinely too much concurrent load for `maximumPoolSize`
- A long-running query/transaction hogging a connection for too long

**Teaches:** this is the single most valuable error to deliberately reproduce (as suggested in the JDBC guide's learning plan) — it makes pool exhaustion, a purely conceptual idea, into a concrete stack trace you can point to.

---

## 7. Statement / SQL Execution Errors

### `SQLSyntaxErrorException: You have an error in your SQL syntax...`

**What's happening:** Exactly what it says — the DB's own SQL parser rejected the query text. JDBC itself does zero SQL validation; it just ships the string to the DB.

**Teaches:** JDBC is a transport mechanism, not a SQL-aware layer — it doesn't parse or validate SQL at all. This is precisely why ORMs like Hibernate exist to catch such errors earlier (at the Java/mapping level) rather than at DB round-trip time.

---

### `ORA-00942: table or view does not exist` / `Table 'db.tablename' doesn't exist` (vendor-specific messages, different per DB)

**What's happening:** Query referenced a table that doesn't exist (or isn't visible to your DB user due to schema/permissions), reported using that vendor's own error format.

**Teaches:** this is exactly why `getSQLState()` exists — `ORA-00942` (Oracle) and MySQL's `"Table doesn't exist"` are both really the _same_ general problem, but expressed in completely vendor-specific formats/codes. SQLState gives you a portable code across vendors; the raw message/error code does not.

---

### `PSQLException: ERROR: column "x" does not exist` (Postgres) — but works if you quote the column name

**What's happening:** Postgres folds unquoted identifiers to lowercase; if your DDL created a column with mixed/upper case using quotes, an unquoted reference in a later query won't match.

**Teaches:** SQL identifier case-folding behavior is vendor-specific — another example of "JDBC doesn't protect you from vendor SQL dialect differences," reinforcing that JDBC standardizes the _API_, not the _SQL dialect_ itself.

---

### `Parameter index out of range (X > number of parameters, which is Y)`

**What's happening:** You called `ps.setX(n, value)` with an index `n` that doesn't correspond to any `?` placeholder in your SQL — usually an off-by-one error, or a placeholder was accidentally deleted/added during editing.

**Teaches:** `PreparedStatement` parameter indices are also **1-based** (matching `ResultSet` column indices) — reinforcing that "1-based everywhere in JDBC" pattern rather than it being a one-off quirk.

---

### `SQLException: No value specified for parameter N` (executing before setting all `?` placeholders)

**What's happening:** You built a `PreparedStatement` with `N` placeholders but only called `setX()` for some of them before calling `execute()`.

**Teaches:** unlike a `Statement`'s plain string SQL, `PreparedStatement` tracks and requires values for _every_ declared parameter slot before execution — it's not just string substitution, it's a stricter, stateful contract with the driver.

---

### `Column 'x' not found` from `rs.getString("x")`

**What's happening:** typo in column name/alias, OR (very common gotcha) you used `SELECT *` and an unexpected column ordering/renaming happened via a JOIN, OR you're using an alias in SQL but querying by the original name (or vice versa).

**Teaches:** `ResultSet` column-name lookups depend on exactly what the DB _labeled_ each output column (via `AS` aliases or default naming), not on your source table's schema names — the mapping is driven by the query's output metadata, which is why `ResultSetMetaData` exists as a way to inspect it programmatically instead of guessing.

---

### Getting `0` instead of an expected value, with no exception thrown

**What's happening:** Reading a SQL `NULL` column via a primitive getter (`getInt`, `getDouble`, `getBoolean`, etc.) — see JDBC guide section 7. Not an exception at all, which is exactly what makes it dangerous — it's a **silent** logic bug, not a loud stack trace.

**Teaches:** this is the most instructive JDBC gotcha precisely _because_ there's no error to Google — the "error" is a wrong value your business logic silently propagates. It's why `wasNull()` exists at all, and it teaches you to be suspicious of any primitive getter on a nullable column.

---

## 8. ResultSet Navigation Errors

### `Invalid operation for forward only resultset` / `ResultSet is TYPE_FORWARD_ONLY`

**What's happening:** Called `rs.previous()`, `rs.first()`, `rs.absolute()`, etc. on a default (forward-only) `ResultSet`.

**Teaches:** cursor type isn't just a performance hint — it's an enforced, hard contract from the driver. You must explicitly opt into `TYPE_SCROLL_INSENSITIVE`/`SENSITIVE` at `Statement` creation time to unlock backward navigation at all.

---

### `ResultSet is closed` (after loop, when accessing again later)

**What's happening:** Tried to read from a `ResultSet` after its parent `Statement` or `Connection` was closed — closing a `Statement` implicitly closes its open `ResultSet`s too.

**Teaches:** JDBC resources have a hierarchy of ownership/lifetime — `Connection` owns `Statement`s, `Statement` owns `ResultSet`s — closing a parent cascades downward. This is exactly why try-with-resources closes them in reverse-declared order (innermost/dependent resource first) rather than an arbitrary order.

---

## 9. Transaction Errors

### `Can't call commit when autocommit=true`

**What's happening:** Called `conn.commit()` without first calling `conn.setAutoCommit(false)`.

**Teaches:** commit/rollback are only meaningful concepts once you've explicitly opted out of the default (auto-commit) mode — reinforcing that "every statement is its own transaction" really is the literal default behavior, not just a description.

---

### `Deadlock found when trying to get lock; try restarting transaction` (MySQL) / similar deadlock errors in other DBs

**What's happening:** Two (or more) transactions each hold a lock the other needs, and the DB detects the cycle and forcibly kills one of them (its JDBC call throws this exception) to break the deadlock.

**Teaches:** concurrency-related transaction issues aren't hypothetical isolation-level trivia — they manifest as real thrown exceptions your app must be ready to catch and often retry. This is exactly the kind of failure `TRANSACTION_SERIALIZABLE` trades performance to avoid more aggressively (at the cost of _more_ such conflicts being detected/blocked, ironically).

---

### Rolled-back transaction, but changes still visible to _your own_ later reads

**What's happening:** Usually not really a bug — reveals a misunderstanding of **connection reuse in pools**: if you didn't actually roll back on the same `Connection` that made the changes (e.g., grabbed a different pooled connection to check), you're just looking at another session that never saw the uncommitted changes to begin with, or the previous connection auto-committed before you realized.

**Teaches:** transactions are scoped to a single `Connection`/session — a classic misunderstanding is expecting transaction state to be somehow global, when it's tied to that one live connection object.

---

### `Batch entry X ... was aborted` / `BatchUpdateException`

**What's happening:** One (or more) statements within a batch failed (e.g., constraint violation on row 500 of 1000), and the driver reports it via a special `BatchUpdateException` — a subclass of `SQLException`.

**Key detail:** `BatchUpdateException.getUpdateCounts()` returns an array showing how many statements _before_ the failure succeeded (behavior differs by driver on whether it stops at first failure or continues).

**Teaches:** batch execution isn't automatically atomic on its own — batching is a network/performance optimization, not a transactional guarantee. You still need explicit `commit()`/`rollback()` around the whole batch if you want all-or-nothing semantics; the JDBC guide's batching example works specifically _because_ it pairs batching with manual transaction control.

---

## 10. Common "Silent" Non-Errors (arguably the most educational — no exception is thrown, but something's wrong)

These deserve special attention because you can't Google a stack trace for them — you have to already suspect the mechanism.

|Symptom|Underlying mechanism|
|---|---|
|Query returns stale data even after another process updated the row|Your transaction's isolation level (e.g., `REPEATABLE_READ`) is deliberately giving you a consistent snapshot — not a bug, but a direct, visible demonstration of what isolation levels actually do|
|App slows down over time, eventually hangs entirely|Connection leak slowly starving the pool — same root cause as the loud `Connection timeout` error above, just manifesting gradually instead of immediately|
|Data "disappears" after app restart|Forgot to `commit()` (auto-commit was off) — changes existed only within an uncommitted transaction tied to a connection that then closed, implicitly rolling back|
|Maven build "just uses an old jar" despite you publishing a new version with the same number|You reused a non-SNAPSHOT (release) version number — Maven treats release versions as permanently cacheable/immutable and won't recheck, unlike `-SNAPSHOT` versions|

---

## 11. How to Use This List

Don't just memorize fixes. For each error above, the real exercise is: **can you explain, without looking, why the fix works mechanically?** If someone says "I added `serverTimezone=UTC` to fix the connection error," you should be able to explain _why_ that parameter exists and what the driver does differently once it's present — not just that adding it "made the error go away."

That's the difference between knowing a Stack Overflow answer and actually understanding Maven/JDBC — and it's exactly the kind of understanding that holds up in an interview when they ask a follow-up "why" question.