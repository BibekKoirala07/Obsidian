
2. Geospatial indexes
    
3. Query optimization
    
4. Explain plans
    
5. Index design strategy
    
6. Common mistakes
    
7. Real-world examples


Operations complexity:

- Search → O(log n)
    
- Insert → O(log n)
    
- Delete → O(log n)
    

# 7. Text Index

For full-text search.

Create:

```js
db.posts.createIndex({
  title: "text",
  content: "text"
})
```

Search:

```js
db.posts.find({
  $text: {
    $search: "mongodb indexing"
  }
})
```

Features:

- Stemming
    
- Tokenization
    
- Relevance scoring
    

Get score:

```js
db.posts.find(
  {
    $text: { $search: "mongodb" }
  },
  {
    score: { $meta: "textScore" }
  }
)
.sort({
  score: { $meta: "textScore" }
})
```

# 8. Geospatial Indexes

For location-based queries.

## 2dsphere Index

```js
db.places.createIndex({
  location: "2dsphere"
})
```

Document:

```js
{
  name: "Cafe",
  location: {
    type: "Point",
    coordinates: [85.3240, 27.7172]
  }
}
```

Nearby search:

```js
db.places.find({
  location: {
    $near: {
      $geometry: {
        type: "Point",
        coordinates: [85.3240, 27.7172]
      },
      $maxDistance: 5000
    }
  }
})
```
# 13. Hashed Index

Used mainly for sharding.

```js
db.users.createIndex({
  userId: "hashed"
})
```

Good for:

- Even distribution
    

Bad for:

- Range queries
    

# 14. Wildcard Index

Indexes dynamic fields.

```js
db.products.createIndex({
  "$**": 1
})
```

Useful for flexible schema.

But:

- Large
    
- Slower
    
- Memory heavy
    

---

# 15. Hidden Index

Test index safely.

```js
db.users.createIndex(
  { age: 1 },
  { hidden: true }
)
```

MongoDB maintains it but query planner ignores it.

Useful for testing before dropping indexes.
# 21. Write Cost of Indexes

Indexes improve reads BUT slow writes.

Each insert/update must update indexes.

Too many indexes:

- More RAM
    
- Slower inserts
    
- Slower updates
    
- Larger storage
    

---

# 22. Memory & RAM

Indexes should fit in RAM ideally.

Why?

- Disk access slow
    
- RAM access fast
    

Monitor:

- Working set
    
- Index size
    
# 25. Query Planner

MongoDB chooses best index automatically.

See winning plan:

```js
db.users.find({
  age: 22
}).explain()
```

---

# 26. Hint()

Force index usage.

```js
db.users.find({
  age: 22
}).hint({ age: 1 })
```

Useful for debugging.

---

# 27. Index Build Types

## Foreground Build

- Faster
    
- Blocks operations (older versions)
    

## Background Build

- Non-blocking
    
- Slower
    

Modern MongoDB improved this significantly.

---

# 28. Reindexing

Rebuild indexes:

```js
db.users.reIndex()
```

Rarely needed now.

---

# 29. Sharding & Indexing

Shard key should:

- High cardinality
    
- Even distribution
    
- Frequently queried
    

Example:

```js
{
  userId: "hashed"
}
```


## Monitor slow queries

# 33. Performance Workflow

When query is slow:

## Step 1

Run explain()

## Step 2

Check:

- COLLSCAN?
    
- docs examined?
    
- keys examined?
    

## Step 3

Design proper compound index

## Step 4

Retest

# 34. Advanced Concepts

## Index Compression

MongoDB compresses indexes internally.

## WiredTiger Engine

Uses cache efficiently.
# 36. Most Important Things to Master

If you truly want MongoDB indexing mastery, deeply understand:

1. B-Tree structure
    
2. Compound indexes
    
3. ESR rule
    
4. Explain plans
    
5. Covered queries
    
6. Cardinality
    
7. Selectivity
    
8. Multikey indexes
    
9. Sorting with indexes
    
10. Query patterns
    



# The REAL Senior MongoDB Indexing Topics

1. **B-Tree Internals**
    
2. **Compound Index Design**
    
3. **Query Planner**
    
4. **Explain Plan Reading**
    
5. **Index Selectivity & Cardinality**
    
6. **Covered Queries**
    
7. **Multikey Indexes**
    
8. **Sorting with Indexes**
    
9. **Aggregation + Index Optimization**
    
10. **Partial vs Sparse Indexes**
    
11. **Text Index vs Atlas Search**
    
12. **Sharding + Index Design**
    
13. **Write Amplification**
    
14. **Memory/Cache Behavior**
    
15. **Index Intersection**
    
16. **Plan Cache**
    
17. **TTL / Hashed / Wildcard Indexes**
    
18. **Slow Query Optimization**
    
19. **Real-world Query Pattern Design**
    

# MongoDB Indexing Topics (Senior-Level Roadmap)

## 1. Index Internals

- B-Tree architecture
    
- Node splitting
    
- Tree balancing
    
- Disk locality
    
- Pointer traversal
    
- WiredTiger index storage internals
    
- Page management
    
- Cache interaction
    
- Prefix compression
    
- Index key encoding
    
- Internal vs leaf nodes
    
- Fill factor concepts
    
- Read amplification
    
- Write amplification
    
- Memory amplification
    

---

# 2. Query Planner Deep Dive

- Query planner architecture
    
- Winning plan selection
    
- Candidate plan generation
    
- Multi-planner
    
- Plan ranking
    
- Trial execution
    
- Plan cache
    
- Replanning triggers
    
- Query shape hashing
    
- SBE (Slot-Based Execution Engine)
    
- Cost-based optimization
    
- Heuristic optimization
    
- IXSCAN internals
    
- FETCH stage internals
    
- SORT stage behavior
    
- LIMIT pushdown
    
- Predicate pushdown
    

---

# 3. Explain Plan Mastery

- executionStats analysis
    
- allPlansExecution
    
- stage tree interpretation
    
- totalDocsExamined
    
- totalKeysExamined
    
- indexBounds interpretation
    
- scanAndOrder
    
- covered query verification
    
- blocking sort detection
    
- memory sort vs index sort
    
- rejected plans analysis
    
- executionTimeMillis
    
- seeks vs scans
    
- cursor behavior
    

---

# 4. Compound Index Theory

- Prefix principle
    
- ESR rule
    
- Equality distribution
    
- Range termination
    
- Compound sort optimization
    
- Reverse traversal
    
- Nested field compounds
    
- Sparse compound indexes
    
- Unique compound constraints
    
- Compound multikey limitations
    
- Index intersection vs compound
    
- Selectivity ordering
    
- Cardinality-driven design
    
- Query shape matching
    

---

# 5. Multikey Index Internals

- Array indexing mechanics
    
- Cartesian explosion
    
- Nested array indexing
    
- Multikey path tracking
    
- Array field bounds
    
- Positional operators
    
- $elemMatch optimization
    
- Compound multikey restrictions
    
- Multikey sort behavior
    
- Array traversal costs
    

---

# 6. Covered Query Engineering

- True covered queries
    
- Projection optimization
    
- _id exclusion mechanics
    
- Index-only execution
    
- Covered aggregation pipelines
    
- Covered sorting
    
- Covered count queries
    
- FETCH elimination
    

---

# 7. Advanced Index Types

- Partial indexes
    
- Sparse indexes
    
- Hidden indexes
    
- Wildcard indexes
    
- Hashed indexes
    
- TTL indexes
    
- Clustered indexes
    
- Columnstore indexes
    
- Case-insensitive indexes
    
- Collation-aware indexes
    
- Text indexes
    
- Atlas Search indexes
    
- Vector Search indexes
    

---

# 8. Text Search Internals

- Inverted index architecture
    
- Tokenization
    
- Stemming
    
- Stop words
    
- TF-IDF scoring
    
- Text score calculation
    
- Language analyzers
    
- Search relevance
    
- Compound text limitations
    
- Text index storage cost
    
- Atlas Search vs text indexes
    

---

# 9. Geospatial Indexing

- 2d indexes
    
- 2dsphere indexes
    
- S2 geometry library
    
- GeoJSON internals
    
- Great-circle calculations
    
- Spatial partitioning
    
- Bounding box optimization
    
- Geospatial query planner
    
- Compound geo indexes
    
- Near queries
    
- Polygon intersection algorithms
    

---

# 10. Columnstore Indexes

- Columnar storage theory
    
- Compression advantages
    
- Analytical query optimization
    
- Projection acceleration
    
- Aggregation acceleration
    
- OLTP vs OLAP tradeoffs
    
- Predicate pruning
    
- Sparse column storage
    

---

# 11. Index Selectivity Engineering

- Cardinality analysis
    
- Histogram concepts
    
- Data distribution
    
- Low-selectivity pitfalls
    
- Entropy concepts
    
- Selective predicate ordering
    
- Frequency skew
    
- Query fan-out
    

---

# 12. Sorting Optimization

- In-memory sort
    
- External sort
    
- Blocking sort stages
    
- Sort spill to disk
    
- Compound sort indexes
    
- Reverse index traversal
    
- Sort merge optimization
    
- Top-K optimization
    
- Memory thresholds
    

---

# 13. Aggregation + Indexes

- $match pushdown
    
- Pipeline optimization
    
- Index-assisted aggregation
    
- SORT + MATCH coalescing
    
- Group optimization
    
- Faceted aggregation costs
    
- Covered pipelines
    
- Aggregation cursor behavior
    
- Disk spilling
    
- Streaming group optimization
    

---

# 14. Sharding + Indexing

- Global vs local indexes
    
- Shard key design
    
- Compound shard indexes
    
- Hashed sharding
    
- Zone sharding
    
- Chunk migration effects
    
- Scatter-gather queries
    
- Targeted operations
    
- Shard key cardinality
    
- Monotonic shard key issues
    
- Balancer impact
    
- Jumbo chunks
    

---

# 15. Replication + Indexes

- Secondary index builds
    
- Initial sync indexing
    
- Oplog replay behavior
    
- Replication lag impact
    
- Read preference optimization
    
- Hidden secondary testing
    
- Rollback behavior
    
- Foreground/background build replication
    

---

# 16. Index Build Mechanics

- Foreground builds
    
- Background builds
    
- Online index builds
    
- Two-phase index builds
    
- Commit quorum
    
- Build locks
    
- Concurrent build behavior
    
- Resumable index builds
    
- Build failure recovery
    

---

# 17. Memory & Cache Behavior

- Working set theory
    
- Hot vs cold indexes
    
- Cache eviction
    
- WiredTiger cache internals
    
- Dirty pages
    
- Read locality
    
- Disk seek optimization
    
- NUMA effects
    
- RAM sizing for indexes
    

---

# 18. Performance Engineering

- Slow query analysis
    
- Profiling system
    
- FTDC metrics
    
- Query latency decomposition
    
- Benchmarking methodology
    
- Throughput vs latency tradeoffs
    
- Read-heavy optimization
    
- Write-heavy optimization
    
- Hybrid workload tuning
    

---

# 19. Write Path Costs

- Insert amplification
    
- Update amplification
    
- Delete amplification
    
- Index maintenance overhead
    
- Page splits
    
- Journal interaction
    
- Bulk write optimization
    
- Batched updates
    
- Write contention
    

---

# 20. Concurrency & Locking

- Document-level locking
    
- Intent locks
    
- Collection locks
    
- Metadata locks
    
- Index build locking
    
- Yielding behavior
    
- Concurrent scans
    
- Latch contention
    
- Read/write conflicts
    

---

# 21. Plan Cache Internals

- Query shape normalization
    
- Cache eviction
    
- Plan cache poisoning
    
- Parameter sensitivity
    
- Replanning thresholds
    
- Cache invalidation
    
- Adaptive planning
    

---

# 22. Schema Design + Index Synergy

- Embedding vs referencing
    
- Access pattern design
    
- Bucket pattern
    
- Outlier pattern
    
- Polymorphic schema indexing
    
- Time-series schema optimization
    
- Event sourcing indexes
    
- High-write schema tuning
    

---

# 23. Time-Series Indexing

- Bucket compression
    
- Time-series collections
    
- Meta field indexing
    
- Bucket pruning
    
- TTL interaction
    
- High-ingestion optimization
    
- Time-window queries
    

---

# 24. Atlas Search & Vector Search

- Lucene internals
    
- BM25 ranking
    
- Semantic search
    
- Vector embeddings
    
- ANN algorithms
    
- HNSW indexing
    
- Hybrid search
    
- RAG optimization
    
- Similarity metrics
    
- Embedding dimensionality
    

---

# 25. Operational Index Management

- Index lifecycle management
    
- Unused index detection
    
- Rolling index deployments
    
- Online migrations
    
- Capacity planning
    
- Index observability
    
- Disaster recovery implications
    
- Backup/restore index behavior
    

---

# 26. Anti-Patterns

- Over-indexing
    
- Redundant indexes
    
- Prefix duplication
    
- Low-cardinality indexing
    
- Regex without prefix
    
- Unbounded array indexing
    
- Large document indexing
    
- Wildcard misuse
    
- Massive compound indexes
    

---

# 27. Real Senior-Level Scenarios

- Designing indexes for billion-document collections
    
- Multi-tenant indexing strategies
    
- Feed/timeline query optimization
    
- Financial transaction workloads
    
- IoT time-series optimization
    
- E-commerce faceted search
    
- High-frequency trading read paths
    
- Social graph traversal optimization
    
- Logging platform indexing
    
- Event-stream analytics
    

---

# 28. MongoDB Engine Internals

- WiredTiger architecture
    
- BSON storage layout
    
- Record store internals
    
- Compression algorithms
    
- Journaling internals
    
- Checkpointing
    
- MVCC mechanics
    
- Snapshot reads
    
- Durable history
    

---

# 29. Distributed Systems Topics

- CAP implications
    
- Consistency models
    
- Read concern interactions
    
- Write concern effects
    
- Distributed query planning
    
- Cross-shard sorting
    
- Distributed aggregation
    
- Chunk balancing internals
    

---

# 30. Principal Engineer / Architect Topics

- Capacity estimation models
    
- Cost-aware index design
    
- SLA/SLO driven optimization
    
- Multi-region indexing
    
- Cloud storage implications
    
- Hot partition mitigation
    
- Workload isolation
    
- Resource governance
    
- Production observability
    
- Failure-mode engineering
    
- Index evolution strategy
    
- Data lifecycle architecture


