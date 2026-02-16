Even if you don’t write SQL here, **this is core SQL knowledge**.

- ACID in real systems
    
- Isolation levels (READ COMMITTED vs REPEATABLE READ)
    
- Phantom reads (conceptual)
    
- Row-level vs table-level locks
    
- Deadlocks (why ordering matters)
    

Interviewers LOVE this.


##### Phase 4 – Transactions (Postgres superpower)

###### 7️⃣ ACID transactions (you’ll love this)

sql

Copy code

`BEGIN;  INSERT INTO orders (...) VALUES (...); UPDATE users SET balance = balance - 100 WHERE id = ...;  COMMIT;`

Or rollback automatically on error.

Mongo needs:

- Sessions
    
- Replica sets
    
- Careful logic
    

Postgres: **native and default**

---

###### 8️⃣ Isolation levels (important later)

- READ COMMITTED (default)
    
- REPEATABLE READ
    
- SERIALIZABLE
    

Postgres prevents:

- Dirty reads
    
- Lost updates
    
- Phantom reads (depending on level)
    

