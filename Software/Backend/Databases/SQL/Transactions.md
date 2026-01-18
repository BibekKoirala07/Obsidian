### Transactions
A sequence of SQL operation that PG treats as one atomic unit. If all operation succeed -> `COMMIT`. If not `ROLLBACK`.

MVCC + WAL + backend process all exists to make transaction possible, safe and fast. 

#### Step-by-step Explanation

#### 1️⃣ Node Client starts a transaction

`BEGIN;`

- Node client tells backend: **“Start a transaction snapshot for me”**
    
- Backend process now tracks **all queries in this transaction**
    
- **MVCC snapshot** is taken → defines which row versions this transaction can see
    

---

#### 2️⃣ Queries are executed

`UPDATE users SET balance = balance - 100 WHERE id = 1; UPDATE users SET balance = balance + 100 WHERE id = 2;`

- Backend creates **new row versions** (MVCC) for updates
    
- Writes **all changes to WAL first**
    
- These changes are **not yet visible to other transactions**
    

---

#### 3️⃣ COMMIT or ROLLBACK

- **COMMIT**
    
    - Backend applies changes to actual table on disk
        
    - Old row versions can be cleaned later by VACUUM
        
    - Changes become visible to other clients
        
- **ROLLBACK**
    
    - Backend discards uncommitted row versions
        
    - WAL changes from this transaction are ignored
        
    - Table remains exactly as it was at the BEGIN
        

---

#### 4️⃣ Why this is important

- Ensures **atomicity** → either all updates succeed or none
    
- Ensures **isolation** → each transaction sees a consistent snapshot
    
- Works **with MVCC** → concurrent transactions don’t block each other
    
- Works **with WAL** → ensures durability even if Postgres crashes
    


If you like, the **next baby step** after Transactions is **tying it all together**:

- One Node client → backend → MVCC → WAL → Transactions
    
- **Then you can safely run SQL queries with full understanding of what Postgres does behind the scenes.**


##### Phase 4 – Transactions (Postgres superpower)

###### 7️⃣ ACID transactions (you’ll love this)

`BEGIN;  INSERT INTO orders (...) VALUES (...); UPDATE users SET balance = balance - 100 WHERE id = ...;  COMMIT;`

Or rollback automatically on error.

Mongo needs:

- Sessions
    
- Replica sets
    
- Careful logic
    

Postgres: **native and default**

---

###### 8️⃣ Isolation levels (important later)

- READ COMMITTED (default)
    
- REPEATABLE READ
    
- SERIALIZABLE
    

Postgres prevents:

- Dirty reads
    
- Lost updates
    
- Phantom reads (depending on level)


