### Query Performance

![[Pasted image 20240617110645.png]]

###### Whenever we execute the mongo Query, it send the request to mongo DB. The `index` is a efficient data structure for looking up for data. It allows us to be directly in line with data that is to be fetched. 

![[Pasted image 20240617113703.png]]

###### Whenever the index is created for collection, it targets the individual property that exist on those records. But we can target multiple property together or consider different property also . Let's say we have indexing for `_id` property. That means if we ever asks for blog post for some `_id`, DB can easily get that blog post quickly as we have indexing for `_id`. If we query using `title`, and if there is no such property in indexing, then we don't enjoy the privilege of fast query. We have to resort back to `full collection scan` and will take time. There are two ways to avoid this.
###### 1. First Way is to add in index for given field. However it reduces the write ability in  collection and we may require to query randomly many times. So, it is not attractive solution
###### 2. Another way to by setting up cache server.

### How does query works 
##### Normal Query
![[Pasted image 20240617130301.png]]
##### Query with Cache Server
![[Pasted image 20240617130326.png]]
###### When we make a query from mongoose, then at first, we check for cache server if that exact query been executed before. If it hasn't then mongoDB execute it and result of that query is stored in cache server. Now, Anytime query is executed, same thing is repeated

###### When query doesn't hit and miss cache server
![[Pasted image 20240617153601.png]]
###### After query is ended as that query is added to Cache Server
![[Pasted image 20240617153623.png]]

