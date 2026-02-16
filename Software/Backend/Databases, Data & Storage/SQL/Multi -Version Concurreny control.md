What are the problem that DB faces ? They are consistency(client shouldn't see half baked data), Concurrency(Many client can exist in same time) and Performance(Locks are expensive). To solve this problem we may block read(other writes couldn't happen at same time) or block writes(read couldn't happen at same time) but think for a second. It makes DB slow and scales terribly. So, MVCC exists to avoid this ==read problem== Like when writes are being occurred, read should be possible at same time. But when writes are being occurred, another writes shouldn't be possible. 

==One thing to remember is that row version in PG is immutable(means rows don't change at all)==. Whenever we are Inserting something like 
```sql
	INSERT INTO users (id, name) VALUES(1, bibek) // TID(Transaction ID) = 100
```
Then PG just don't have id and name column, It has two extra column which are called ==Xmin and Xmax(hidden system column and not logical columns)==. This is quite important. So what do those represent ? It represent the transaction which changed the state of that row. Like this
```sql
id | name | xmin(which transaction created it) | xmax(which transaction made it invalid)
1  | Bibek | 100  | null
```

So how do we get the values for Xmin and Xmax ? Well every transaction creates the Transaction ID(XID). Let's say that Transaction ID for upper `INSERT` command was 100. You see the Xmin there ? Well 100. If Xmax is `null`, then that row is still valid, If it has some `XID`, then that `XID` represent the transaction ID that made it invalid. We can make it invalid by updating that row which don't update that row but creates new one with that values and TID also. 
So, It may looks like this
```sql
id | name | xmin | xmax
1  | Bibek | 101  | null
```
and that upper row with `Xmin` =  `100` is updated with value of `Xmas` = `101`(atomically with new row). Thus making this row "obsolete" and new row being the truth. Now the question may be why does PG keeps that previous row right ? Well it may still be needed for crash recovery, snapshot safety and other transaction might still need it. Later, when no snapshot can view it, VACCUM deletes that row. 

Let's learn with examples. Let's say we have two Node clients connected to PG.
```js
Node Client A → Backend Process #1
Node Client B → Backend Process #2
```
And Both Transactions want to read the same data. And Now let's say we have this. 
```sql
id | name | Xmin  | Xmax
1  | bibek | 100  | NULL    T == 100
```
and After Transaction 101 ID and name is updated to `aneel`.
```sql
id | name | Xmin  | Xmax
1  | bibek | 100  | 101    T == 100
1  | aneel | 101  | NULL   T == 101
```
So Let's say Client B starts little bit earlier where snapshot didn't include  Transaction 101. So T101 isn't visible for Client B snapshot. Visibility is based on snapshot and committed transaction. B will see that T100 is valid and will give that result but Client A will see the new row version because the old version is marked invalid (`xmax=101`).

So there was no locking the rows at all. Read just check meta data (`Xmin` and `Xmax`).

But what if two writes happens at same time ? Both will want to set `Xmax` ? So they must be serialized. Here PG uses row-level lock. ==So MVCC avoid read locks, not write conflicts==