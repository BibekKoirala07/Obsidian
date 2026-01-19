### Vertical and Horizontal Scaling


- Vertical vs horizontal scaling
    
- Read replicas
    
- Write bottlenecks
    
- Sharding basics (when SQL alone breaks)
    
- OLTP vs OLAP workload separation



### 🔹 Vertical vs Horizontal Scaling

**Vertical scaling**

- Increase CPU/RAM/Storage
    
- Simple but limited
    
- Best for OLTP small-medium systems
    

**Horizontal scaling**

- Add more servers
    
- Requires sharding/partitioning
    
- Best for high-traffic systems
    

###### 🔹 Read Replicas

- Used to scale reads
    
- Master handles writes
    
- Replicas handle reads
    
- **Lag is important** (replication delay)
    

### 🔹 High Availability (HA)

- Failover mechanisms
    
- Multi-AZ deployments
    
- Standby replicas
    
- Automated failover
    

### 🔹 CAP Theorem (Basic but important)

- **Consistency, Availability, Partition tolerance**
    
- SQL DBs usually prefer **Consistency + Partition tolerance** (CP)
    
- Some systems use **Read replicas = eventual consistency**
    

---

## 2. **Indexes (Most Important for Performance)**

### 🔹 Types of Indexes

- **B-Tree (default)**
    
- **Hash index** (rare, special cases)
    
- **GIN / GiST** (full text, arrays)
    
- **BRIN** (large tables, sorted data)
    

### 🔹 Indexing Rules

- Index columns used in:
    
    - WHERE
        
    - JOIN
        
    - ORDER BY
        
    - GROUP BY
        
- Use **composite indexes** for multi-column queries
    
    - Order matters (high cardinality first)
        

### 🔹 Index Problems

- Too many indexes → slower writes
    
- Index bloat
    
- Index not used due to poor query structure
    

### 🔹 Partial Index

- Index only subset of rows
    
- Useful for “active” records  
    Example:  
    `WHERE status = 'active'`
    

---

## 3. **Query Optimization**

### 🔹 EXPLAIN / EXPLAIN ANALYZE

- Shows execution plan
    
- Helps identify:
    
    - Seq scan vs index scan
        
    - Join types
        
    - Cost, estimated rows
        

### 🔹 Common Bottlenecks

- **Sequential scan** on large tables
    
- **Nested loop joins** with large datasets
    
- **Sort operations** on big data
    
- **Function calls in WHERE clause**
    

### 🔹 Query Tuning Techniques

- Rewrite queries
    
- Avoid SELECT *
    
- Use LIMIT properly
    
- Denormalize when needed
    
- Use materialized views
    

---

## 4. **Partitioning**

### 🔹 What is Partitioning?

Splitting a big table into smaller pieces (partitions).

### 🔹 Partition Types

- **Range partitioning** (by date)
    
- **List partitioning** (by category)
    
- **Hash partitioning** (even distribution)
    

### 🔹 Benefits

- Faster queries on partitioned column
    
- Faster deletes (drop partition)
    
- Better maintenance
    

---

## 5. **Concurrency & Isolation**

### 🔹 Transaction Isolation Levels

- **Read Uncommitted** (dirty reads)
    
- **Read Committed** (default in Postgres)
    
- **Repeatable Read**
    
- **Serializable**
    

### 🔹 Locking

- Row locks vs table locks
    
- Deadlocks
    
- Long transactions cause blocking
    

### 🔹 Optimistic vs Pessimistic Locking

- Optimistic: check version column
    
- Pessimistic: lock rows
    

---

## 6. **Replication & Backup**

### 🔹 Replication

- Streaming replication (Postgres)
    
- Logical replication
    
- Hot standby
    

### 🔹 Backups

- Base backup + WAL logs
    
- Point-in-time recovery (PITR)
    
- Always test restore
    

---

## 7. **Monitoring & Observability**

### 🔹 What to monitor?

- Query latency
    
- Cache hit ratio
    
- Connection count
    
- Locks / deadlocks
    
- Replication lag
    
- Disk I/O
    
- CPU & memory usage
    

### 🔹 Tools

- **pg_stat_statements**
    
- **pg_stat_activity**
    
- Prometheus + Grafana
    
- Datadog / New Relic
    

---

## 8. **Reliability & Maintenance**

### 🔹 Routine Maintenance

- VACUUM (Postgres)
    
- REINDEX
    
- ANALYZE / auto-analyze
    
- Partition maintenance
    

### 🔹 Preventing Issues

- Avoid long transactions
    
- Avoid huge unindexed scans
    
- Maintain indexes
    
- Keep statistics updated
    

---

## 9. **Data Modeling for Scale**

### 🔹 Normalization vs Denormalization

- Normalization: avoids duplication, best for correctness
    
- Denormalization: improves read performance
    

### 🔹 Use Cases

- OLTP: normalized
    
- Analytics: denormalized / star schema
    

### 🔹 Star Schema / Fact & Dimension

- Fact table = events (high volume)
    
- Dimension tables = attributes (smaller)
    

---

## 10. **Security & Access Control**

### 🔹 Roles & Permissions

- Create roles for:
    
    - read-only
        
    - read-write
        
    - admin
        

### 🔹 Row Level Security (RLS)

- Use when multiple tenants share the same table
    
- Enforce per-user access at DB level
    

### 🔹 JWT + RLS

- App passes JWT claims
    
- DB uses claims to filter rows
    
- Example:
    
    - `SET LOCAL app.current_user_id = ...`
        
    - RLS policy uses `current_setting('app.current_user_id')`
        

---

# 🎯 If you want, I can make this into:

✅ **A full Obsidian note** with sections and links  
✅ **A PDF/Markdown export**  
✅ **A more advanced version for Postgres specifically**  
✅ **Examples for each topic (with SQL code)**

---

## Quick question

Do you want the notes to be **Postgres-specific** or **generic SQL** (works for MySQL, SQL Server too)?


