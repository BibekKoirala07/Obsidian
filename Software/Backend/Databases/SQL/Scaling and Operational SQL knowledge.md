### Scaling and Operation SQL Knowledge

- Vertical vs horizontal scaling
    
- Read replicas
    
- Write bottlenecks
    
- Sharding basics (when SQL alone breaks)
    
- OLTP vs OLAP workload separation



##### 🔹 Implication 2: Predictable failure modes

In Postgres:

- One query crashing ≠ database crashing
    
- One user misbehaving ≠ system outage
    

This is huge in production.

Mongo / thread-based systems:

- Bugs can cascade
    
- Memory corruption can kill the server
    

Postgres fails **locally**, not globally.

---

###### 🔹 Implication 3: OS scheduling matters

Because Postgres relies on the OS:

- CPU scheduling is fair
    
- Context switching is predictable
    
- Kernel handles starvation
    

This is why Postgres:

- Behaves well under load
    
- Degrades gracefully
    
- Rarely “locks up” completely
##### Reason 2: Shared memory + processes scale predictably

Postgres uses:

- Processes for execution
    
- Shared memory for coordination
    

This means:

- Less complex locking logic
    
- Fewer race conditions
    
- Easier correctness proofs
    

Correctness > cleverness.

---

##### Reason 3: Simpler mental model for concurrency

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

##### Why this matters in real life (not theory)

##### Scenario: traffic spike

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

###### 5️⃣ Why not everyone chooses this design?

Because it has tradeoffs.

<<<<<<< HEAD
###### Downsides of process-based design
=======
##### Downsides of process-based design
>>>>>>> 033a4c318f2802741eb19da913d6736a13d89bae

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

#### 6️⃣ Why _you_, personally, should care

Because this affects: