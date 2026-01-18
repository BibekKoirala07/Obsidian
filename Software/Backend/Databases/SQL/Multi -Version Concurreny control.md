What are the problem that DB faces ? They are consistency(client shouldn't see half baked data), Concurrency(Many client can exist in same time) and Performance(Locks are expensive). To solve this problem we may block read(other writes couldn't happen at same time) or block writes(read couldn't happen at same time) but think for a second. It makes DB slow and scales terribly. So, MVCC exists to avoid this ==read problem== Like when writes are being occurred, read should be possible at same time. But when writes are being occurred, another writes shouldn't be possible. 

Let's learn with examples. Let's say we have two Node clients connection to PG
```js
Node Client A → Backend Process #1
Node Client B → Backend Process #2
```
And Both want to read/write the same row at the same time. Well it is a problem now. It can be solved using MVCC. 
Every row in PG can have multiple version at the same time. When we update a row, PG creates a new version instead of overwriting the old one immediately. When we read the row, PG shows the version valid of our transaction. 
Let's say we have row in table
```
id | name | age | version
1  | John | 23  | v1
```
Now Client A want to update the age to 24. PG does this
```
id | name | age | version
1  | John | 23  | v1   <- old version, still readable
1  | John | 24  | v2   <- new version, for Client A's transaction
```
Meanwhile Client B can still read `v1`, because that was valid when it started its transaction( reading process in this context, transaction just means sequence of operations that PG treats as one atomic unit). So the main philosophy is reads don't block writes and writes don't block reads.  However, Writes in PG do block when other writes on the same row. 

So, Old versions stick around until PG cleans them(VACUUM).

So in terms of PG, Update = Delete + Insert
