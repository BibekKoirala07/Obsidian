Okay, so you're further along than the average plateau. Let me reframe what you actually need.

You're not missing _knowledge_ — you're missing **operational and architectural depth**. The stuff that only matters when things go wrong, scale up, or get complicated in production.

---

**What you're actually missing sounds like:**

**Replica Sets & High Availability**

- How elections work when a primary goes down
- Read preferences (`primary`, `primaryPreferred`, `secondary`, etc.) and when you'd actually use them
- Write concern (`w:1`, `w:majority`, `w:0`) and what you're trading off — speed vs. durability
- Oplog — what it is, how replication actually works under the hood
- What happens during a network partition (this connects to CAP theorem)

**Sharding**

- When you actually need it vs. when you're just over-engineering
- Shard key selection — this is genuinely hard and a bad choice is nearly impossible to fix later
- Hashed vs. ranged sharding and the hotspot problem
- Chunks, balancers, mongos routers

**Transactions in depth**

- Multi-document ACID transactions — their real performance cost
- When denormalization is architecturally better than a transaction
- Session management

**Change Streams**
- What they are (real-time event stream off the oplog)
- Practical uses — event-driven architecture, cache invalidation, audit logs

**Performance in depth**
- Query planner internals — how MongoDB picks an index
- `explain()` output — actually reading `executionStats` and `queryPlanner` stages
- Index intersection, partial indexes, sparse indexes, TTL indexes
- The working set concept — why RAM matters more than disk for MongoDB performance

---

**The best way to actually learn this now**

Since you've already read patterns and know the basics — **don't read more, build more deliberately.**

- Spin up a 3-node replica set locally with Docker. Kill the primary. Watch what happens. This teaches you more than any article.
- Use `explain()` obsessively on your current job's queries
- Read **"MongoDB: The Definitive Guide"** (Shannon Bradshaw) — specifically the chapters on replication, sharding, and administration. Skip what you know.

The gap you have is the **infrastructure and internals layer** — and that's learned by breaking things in a safe environment, not by reading.

Yes, understood completely. No aggregation, no query syntax. Pure **architecture, internals, and operations**.

---

**1. Replication**

- How the oplog works internally
- Election process — how a primary is chosen, what triggers it
- Read preferences and when to use each
- Write concern — `w:1`, `w:majority`, `w:0` and durability tradeoffs
- Replication lag — what causes it, how to monitor it
- What happens during network partition (CAP theorem applied to MongoDB)
- Rolling upgrades/maintenance on a replica set

**2. Sharding**

- When sharding is actually needed (vs. vertical scaling)
- Shard key selection — the most critical and irreversible decision
- Hashed vs. ranged sharding
- Hotspot problem and how shard key design causes it
- Chunks and the balancer
- Mongos router — what it does
- Scatter-gather queries and why they kill performance

**3. Indexing Internals**

- How MongoDB's B-tree indexes actually work
- Compound index prefix rule
- Covered queries
- Partial indexes, sparse indexes, TTL indexes, wildcard indexes
- Index intersection
- How the query planner chooses an index
- Reading `explain()` output properly

**4. Transactions**

- How multi-document ACID works in MongoDB
- Real performance cost of transactions
- When to use transactions vs. when to redesign schema instead
- Session management

**5. Change Streams**

- What they are and how they sit on top of the oplog
- Practical use cases — event-driven arch, audit logs, cache invalidation
- Resumability

**6. Storage Engine (WiredTiger)**

- How WiredTiger stores data internally
- Compression
- Cache and the working set concept — why RAM is everything
- Journaling and crash recovery
- Checkpoints

**8. Schema Design at Scale** _(beyond patterns)_

- Schema versioning strategies in production
- Multi-tenancy design approaches
- Time-series collections (native, introduced in MongoDB 5.0)
- Data archival strategies

**9. Monitoring & Operations**

- Key metrics to watch — page faults, replication lag, connections, working set
- MongoDB Atlas monitoring vs. self-hosted
- Backup strategies — mongodump vs. snapshots vs. Atlas
- Capacity planning

---

**Suggested order:** Replication → Sharding → WiredTiger → Indexing Internals → Transactions → Change Streams → Security → Monitoring

Start with Replication because Sharding builds on it, and WiredTiger explains _why_ replication and indexing behave the way they do.

# Replication in MongoDB

## What is a Replica Set

3 or more mongod instances holding the same data. One **primary**, rest are **secondaries**. Simple concept — but the internals are what matter.

---

## The Oplog

This is the heart of replication. Everything else builds on it.

- The oplog (`local.oplog.rs`) is a **capped collection** on every node
- Every write on primary gets recorded as an idempotent operation in the oplog
- Secondaries tail the primary's oplog and replay operations
- **Idempotent** is key — the same oplog entry applied multiple times produces the same result. MongoDB transforms writes to make them idempotent (e.g. `$inc` becomes a `$set` with the final value)
- Oplog has a **size limit** — if a secondary falls too far behind and the oplog rolls over, it goes into **RECOVERING** state and needs a full resync. This is a real operational risk.

**Oplog window** — how far back your oplog covers in time. On a write-heavy system this could be hours. On a light system, days. You should know yours.

---

## Replication Process in Detail

1. Client writes to primary
2. Primary applies write to its data files
3. Primary writes an oplog entry
4. Secondaries are constantly pulling new oplog entries
5. Each secondary applies those entries to its own data

This is **asynchronous by default** — primary doesn't wait for secondaries unless you explicitly ask it to via write concern.

---

## Elections

When primary goes down:

1. Secondaries detect the primary is gone (via heartbeats — sent every 2 seconds)
2. After `electionTimeoutMillis` (default **10 seconds**) a secondary calls for election
3. Candidates need a **majority of votes** to win
4. The secondary with the most up-to-date oplog wins
5. New primary is elected, clients reconnect

**Key things to know:**

- You need an **odd number** of nodes for majority. 2 nodes = bad idea, primary goes down, secondary can't get majority (1 out of 2 isn't majority)
- **Arbiter** — a voting member with no data. Only exists to break ties. Generally avoid in production unless cost is a real constraint
- During election there's a **brief window where writes are rejected** — your app needs to handle this with retryable writes
- `priority` setting — you can make certain nodes more likely to become primary. Useful if nodes are in different regions
- `hidden` members — participate in replication but never become primary, invisible to clients. Good for analytics/backups
- `delayed` members — replicate with a time lag (e.g. 1 hour behind). Your safety net against accidental mass deletes

---

## Write Concern

This is where **durability vs. performance** tradeoff lives.

- `w:0` — fire and forget. Primary doesn't even acknowledge. Fastest, zero durability guarantee
- `w:1` — default. Primary acknowledges after writing to its own memory. Secondary replication is still async
- `w:majority` — primary waits until majority of nodes have written the oplog entry. Slower but survives primary failure without data loss
- `j:true` — waits until write is flushed to journal on disk. Guards against primary crash before in-memory data hits disk

**The real risk of `w:1`:** Primary acknowledges, client thinks write is done, primary crashes before secondary catches up — **that write is gone**. For anything important, `w:majority` is the right call.

---

## Read Preference

Controls which node your reads go to:

- `primary` — default, always reads from primary. Strongly consistent
- `primaryPreferred` — reads from primary, falls back to secondary if primary is down
- `secondary` — always reads from secondary. **Eventually consistent** — you may read stale data
- `secondaryPreferred` — secondary if available, else primary
- `nearest` — lowest network latency, regardless of role

**When you'd actually use secondary reads:**

- Analytics/reporting queries that can tolerate stale data
- Offloading read traffic from primary
- Geographically distributed apps reading from a local node

**The trap:** using `secondary` reads and wondering why you're seeing stale or inconsistent data. Replication lag means secondaries are behind.

---

## Replication Lag

The delay between a write on primary and it appearing on secondary.

**Causes:**

- Secondary under heavy load (disk, CPU)
- Network issues between nodes
- Large write operations that take time to apply
- Oplog application is single-threaded per collection (historically — improved in newer versions with parallel oplog application)

**Why it matters:**

- If you're reading from secondaries, you're reading potentially stale data
- If lag grows too large and oplog rolls over — secondary needs full resync
- Monitor `rs.printReplicationInfo()` and `rs.printSecondaryReplicationInfo()`

---

## Network Partition & CAP Theorem

MongoDB is **CP** — consistency and partition tolerance, sacrifices availability.

**Split brain scenario:** Imagine 3 nodes, network splits into {Primary, Secondary1} and {Secondary2}.

- The side with majority ({Primary, Secondary1}) keeps operating
- Secondary2 can't reach majority, becomes read-only
- This prevents two primaries from accepting writes simultaneously — no split brain

If primary gets isolated with no majority — it steps down. Writes stop. This is the availability sacrifice.

---

## Rollbacks

This is the scary one.

If a primary accepts a write, crashes, and a secondary becomes primary _before_ that write was replicated — when the old primary comes back, it has writes the new primary doesn't know about. Those writes get **rolled back** into a rollback BSON file.

- With `w:majority` this essentially can't happen
- With `w:1` it absolutely can
- Rolled back data isn't lost forever — it's in `rollback/` directory as BSON — but recovering it is manual and painful

---

## What You Should Actually Do With This

- Spin up a 3-node replica set locally with Docker
- Kill the primary and watch the election happen in real time
- Check the oplog size and understand your oplog window
- Try writing with `w:majority` vs `w:1` and measure the latency difference
- Introduce artificial replication lag and read from a secondary — see the stale data yourself

That hands-on hour will cement this better than reading ever will.