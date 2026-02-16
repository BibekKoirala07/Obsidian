At minimum, we must install 3 components. They are
- PostgreSQL Server(postgre/postmaster). The old name is `postmaster`. It is still used conceptually. It listens on port `5432` also spawns backend processes on demand `fork()`. and also manages WAL, Shared buffers, Locks, Transaction, Query execution. Without this nothing works. 
- Client tools(psql, pg_dump, etc.). We can make our own client which is able to talk to PG in postgres protocol but common who does it right ? while `psql` seems to be optional, it is kinda cumpolsory. 
- Data directory(where actual DB file live), PG doesn't store data inside the binary. It stores everything in a data directory often called `PGDATA`. Inside this directory, there are table files, Index files, WAL Logs, System catalogs, Configuration state. It is situated typically in `/var/lib/postgresql/version/main`. If this directory is deleted -> database is gone. 

Everything else is just add-ons or optional.

==A cluster is one PostgreSQL Server tied to single Data Directory==. One cluster shares everything inside it like Databases, background worker process, one postmaster/server process and One Data Directory(`PGDATA`). all the WALs, configs, processes and databases inside one data directory run in one cluster for all the database. However one machine can have multiple cluster. It should have different postmaster, Data directory and its sets of databases. Two cluster are completely separate.  All operation like background workers, transactions, WAL, connections are scoped to one cluster.
==Each Database in cluster has unique Object Id (OID) assigned by PG==. 

PG uses plain text config files 
`postgresql.conf` -> controls port, memory, max_connections, WAL settings, Logging
`pg_hba.conf` -> It controls who can connect and how. 

And PG has its own users. They live inside PostgreSQL. Always remember that OS users != PG users. 

Among some optional tools some are frequently used like
- pgAdmin -> GUI for PostgreSQL
- Backups(`pg_dump`) -> Safety
- Monitoring -> Production

Here’s the **correct next order**, not random tutorials:

1. PostgreSQL **roles, databases, schemas**
    
2. What happens when you run a SQL query internally
    
3. Transactions (`BEGIN / COMMIT / ROLLBACK`)
    
4. Indexes (B-tree, why they matter)
    
5. Query planner (EXPLAIN)
    
6. Locks & isolation levels
    
7. When PgBouncer breaks things (prepared statements, session state)
###### 🔟 What is REQUIRED vs OPTIONAL