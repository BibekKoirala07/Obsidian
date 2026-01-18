What if Client A sends an update ? The backend process executes it in memory and writes it to disk at the same time. But what if PG crashes in the middle of writing the change to disk ? That is seriously bad. 

So before changing the actual table on disk, PG writes the change to the separate log file(WAL). Only after the WAL is safely written, PG applies the changes to the table. If PG crashes, it replays the WAL to recover all committed changes. It is like journal or notebook. 

And every update, insert, delete and DDL operation is logged in WAL.

Let's say PG crashes before applying change to table after writing in WAL .On restart, PG reads the WAL and re-applies all committed changes to table. This is how PG guarantees durability(the D in ACID). If PG shuts down cleanly the WAL replay is usually not needed. 