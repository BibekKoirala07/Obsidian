```sql
CREATE DATABASE dbname
	[  [ WITH ] ]
		[ OWNER [=] db_owner ] // user who created the database
		[ TEMPLATE [=] template_db ] // clogint the existing database. 
		[ ENCODING [=] encoding ]
		[ LC_COLLATE [=] lc_collate ]
		[ LC_CTYPE [=] lc_ctype ]
		[ TABLESPACE [=] tablespace_name ] // controls physical location of database files. Default for PG is pg_default
		[ CONNECTION LIMIT [=] max_connection ] // maximum concurrent connections to this database. Default is no limit(uses global limit). Helps prevent overload in multi-tenant systems. 
```

==One thing to know==
Mostly I thought of Table right after creating Database but that isn't true at all. 
```
PostgreSQL Server
	Database
		Schema
			Table
```
Table don't live directly inside database, Table live inside schema. 

==Schema is a namespace== inside a database that organizes table, views, function, etc. 
Schema solves organization. Instead of multiple table in one place, We can do 
```
public.users
public.orders
auth.users
```
And we can have same table in different collections
```
auth.users
hr.users
```
We can give security/access control to only certain schemas
```sql
GRANT USAGE ON SCHEMA auth TO backend_app;
GRANT SELECT ON ALL TABLES IN SCHEMA auth TO backend_app;
```
We can do logic separation. Like
```
auth -> users, sessions, roles
public -> some tables
audit -> logs
billing -> payments
```
But if we notice, we can see that public schema appear magically. Why ? cuz every database automatically has a schema called public. 
When we create table like this
```sql
CREATE TABLE users () -> CREATE TABLE public.users (postgres automatically assume this)
```
