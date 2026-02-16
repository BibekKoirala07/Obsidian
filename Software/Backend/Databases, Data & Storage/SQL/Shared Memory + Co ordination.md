As we all know PostgreSQL is process based. Which means each client gets it own backend process. Each backend process has its own memory(which know its own transaction, row versions, WAL writes). So each process are independent. So how do independent process know about Which rows are locked ? Which transaction are running ? How to maintain MVCC visibility ? 

A special memory area in PostgreSQL, visible to all backend processes. Like a common whiteboard that all backend can read/write safely. 

Now you may ask why do we need to lock rows right ? But imagine two transactions trying to update the same row. We need some mechanism to tell that "don't write this row yet!". 

It is central Brain. 
