For PostgreSQL, it decides "Who are you ? " and "What can you do?". PG protects four main things. They are
- the server(Who can connect)
- A database
- Objects inside a database like table, views, functions, schemas
- and actions like SELECT, INSERT, UPDATE, DELETE, CREATE, DROP, etc. 
From common sense we can know that PG must answer two question for every connection and query. They are "Who are you ? " and 'What are you allowed to do ? ' By simple logic, Authentication and Authorization. 

==There is no real difference between users and roles==. Historically there were users and groups. Modern PG unified everything under roles. 

PG just has exactly one concepts for identity: ==roles==. A role is just a record with some flags.
Two flags matter right now. 
- **LOGIN** -> Can this role connect to the DB ? 
- **PRIVILEGES** -> What can this role do ? 

So Let's say we have first role with `LOGIN=TRUE`(It is called user). It means that the role can communicate with PG. For example
```
ROLE: app
LOGIN: YES
PRIVILEGES: none
```
Now let's say we have second role with`LOGIN=FALSE`(It is called group). It means that the role can't connect at all. It can only hold permissions which is used by roles with `LOGIN=TRUE`.
For example:
```
ROLE: read
LOGIN: NO
PRIVILEGES:
	- SELECT on users
	- SELECT on comments
```

And the main thing is in PG, one role can inherit another role. It is the gist. 
So, if `app` is inheriting `read`, you can act like the permission of `read` is also being checked. 

So, first client comes in as `app`. PG looks for `LOGIN` whether it is `TRUE` or `FALSE`. If that value is negative, query doesn't move forward. But if that value is positive, then PG looks which permission(group) does that `app` has. but before looking for group, It does look for direct permission if that `app` has it or not. If not it inherit from `read`. And does that permission exists on `read`(group). And it does. so query is allowed. 

And one thing to realise is Authentication happens at connection time
It users password, certificates, OS users, trust and is controlled by `pg_hba.conf`. When client connects to PostgreSQL
```
Client -> TCP -> PG
```
First PG checks `pg_hba.conf` and match connection type(local/host), database, user and client IP. It decides ==auth method==, verify credentials, and thus allow or reject connection. Let's say for example, we have `pg_hba.conf` 
```
host mydb app 0.0.0.0/0 md5
```
host - > TCP connection
mydb -> database name
app -> POSTGRESQL role
0.0.0.0/0 -> any IP
md5 -> password auth

If this blocks the connection, then nothing else matter. 
==pg_hba.conf is checked for authentication for first time, after that no query need to hit that file ever until that connection is terminated== and authentication check happens for each connection in pool. 

For common auth methods, we have
trust(no password), md5(password), scram-sha-256(mosts popular, modern secure password), peer(OS user), cert(TLS certificate)

and Authorization happens after connection. It checks roles and privileges and is controlled by `GRANT/REVOKE`. PostgreSQL objects live inside schemas:
```
database
|--schema public
|   |----table users
|   |----table posts
|--schema private
```
We need permission on schema and permission on object inside it to get it. 

==And Superusers can bypass all permission checks, can read any data and can change system, bypass RLS, ignore ownership rules. It shouldn't be used in production. ==

==Caution==
Sometimes we might see that why is PostgreSQL allowed to login for root user without password ? This is misconception. While we installed the PostgreSQL, During installation, OS user `postgres` was created, DB role `postgres` was also created. We may think that linux user `root` is able to connect to DB without password but no, We have to switch to System User (it is the special kind of Linux user account that exists to run services or daemons, not for a person to log in interactively. )`postgres` for us to connect without password. Otherwise we need password.  
```
sudo -u postgres psql // run a command as linux user postgres and the command is psql
```
After we run this command, `whoami` shows `postgres`. So this is the thing that we need to care about. 

Sometimes we may see that why is sub roles like ebpearls is able to connect with password despite not being given that privilege. Well, may be for that user, we may not need to able to login as We may have trust coming from localhost for `pg_hba.conf`. 

==It is the role that we do login and not other things It is mostly confusing at first but it is true.==


#### Why Postgres does this (ONE reason only, for now)

Because databases:

- Do heavy CPU work
    
- Do blocking I/O
    
- Must isolate failures
    
- Must keep correctness
    

Threads make this _much harder_.

Processes make this **boringly safe**.

Here we will learn about PostgreSQL.

Know this later 
##### confirm who owns the database

Run this as `postgres`:

`SELECT datname, pg_get_userbyid(datdba) AS owner FROM pg_database WHERE datname = current_database();`

If owner ≠ `bibek`, then `bibek` cannot create tables by default.

---

###### 4️⃣ Fix #1 (Recommended): Grant schema permission

Run this as `postgres`:

`GRANT CREATE ON SCHEMA public TO bibek;`


###### 5️⃣ Fix #2 (Best practice): Make `bibek` the database owner

Even better:

`ALTER DATABASE your_db_name OWNER TO bibek;`

Database owner automatically has:

- CREATE on schemas
    
- Full control inside that DB ## 6️⃣ Why PostgreSQL does this (INTERVIEW GOLD ⚠️)

PostgreSQL separates:

- **Database privileges** → connect, create schema
    
- **Schema privileges** → create tables, views, functions
    

> Table creation happens inside a schema, not directly in a database.


###### 8️⃣ Quick mental model (easy)

`Cluster  └── Database       └── Schema            └── Table`

Permissions are checked **at each level**.


❓ _User has CREATEDB but cannot create tables — why?_  
✅ Because CREATE TABLE requires CREATE privilege on the schema.

❓ _How do you fix it?_  
✅ Grant CREATE on schema or make the user database owner.