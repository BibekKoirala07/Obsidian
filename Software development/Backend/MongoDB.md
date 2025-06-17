This are complex MongoDB things.
## Aggregation Pipelines Stages
Aggregation framework is all about building a pipeline of steps that runs on the data that is retrieved from the collection and then gives you the output the form we need. Every stages receives the output of the previous stages.
![[Pasted image 20250616161655.png]]
## Indexing
just a special data structure that MongoDB uses to quickly locate data without scanning every docs in the collections. Instead of flipping through every page to find a term, we just jump straight to it. 

It helps prevent collection scan. We can make index for whatever field is we need to query. Index for db collection is just like index for a book. It lists out everything we need to look up and it tells us where to find. 

Index is the list of values of certain field in our document.

==If there is a field that we do lot of searching and we don't do that much updates, then index is necessary==. 
###### Single field indexes
create index for single field in document. that is it. 
So lets understand it fully.
Lets make index.
```js
db.users.createIndex({ email: 1 })
```
let's say we have `users` collections that looks like this.
```js
[
  { _id: 1, name: "Alice",  email: "a@a.com" },
  { _id: 2, name: "Bob",    email: "b@b.com" },
  { _id: 3, name: "Charlie",email: "c@c.com" }
]
```
MongoDB reads every document in `users`. For each document, it extracts the value of `email` field. and `_id` of the document. It build the intermediate structure
```js
email  -> id
"a@a.com"  -> 1
"b@b.com"  -> 2
"c@c.com"  -> 3
```
After that MongoDB builds a B-tree(sorted from `A` to `Z`).
MongoDB stores this data in B-tree data structure(which is self-balancing). 
```js
			b@b.com

	a@a.com              c@c.com
```
The email value is stored in each node. and a pointer to the document's _id. MongoDB keeps this B-tree sorted for fast binary-style searching. 

MongoDB saves this index on disk as a separate index file on disk alongside your data collection files. 

So, when we hit query like 
```js
	db.users.find({ email: "c@c.com" })'
```
Instead of scanning all the document. it directly compare whether is is ahead or behind in that B+ tree. compares it, takes direction and when the collection is found, it is pointed using `_id` for a document. like if we find `c@c.com` then it points to it's id.

It is great for `sort({ email: 1 })` as it is already available but not for `sort({ email: -1 })` as it needs to be reversed. So, if we already know we need descending data, then doing `createIndex({ email: -1 })` is better as data is already done in reversed/descending order. 

so `1` and `-1` is about how does B-tree is built and how it can support sorting, nothing more. if it is index `-1`.

In short, whatever is indexed field in B+-tree becomes key and MongoDB build an index file like this. Each key points to one or more document IDs that have value. 
```js
age: 12 -> [_id: 1,_id: 3, _id:5],
age: 30 -> [_id: 3],
age: 34 -> [_id: 4]
```
a pointer, usually `_id` so what database can look what those `_id` are. Remember the value in B-tree isn't entire document but rather the pointer which in most case is `_id` and database later goes to those field to look for those. In case of ranges, it starts a some key `25` and walk the tree like `25 -> 26 -> 27 -> 28` and may be to some parts whatever the query has like may be queries has `$gte: , $lte: `. And It collects all the `_id`(value) found under those keys and fetch those docs from collection via `_id`.
###### Compound indexes
let's start with an example
```js
	db.users.createIndex({age: 1, name: 1})
```
This means first sort/index by `age`(primary sort) and for same `age`, by `name`. "Index by age, and for each age bucket, index by name".

For example 
```js
[
  { _id: 1, age: 25, name: "Alice" },
  { _id: 2, age: 25, name: "Bob" },
  { _id: 3, age: 30, name: "Charlie" },
  { _id: 4, age: 30, name: "Daniel" },
  { _id: 5, age: 35, name: "Eve" }
]
```
and let's say compound index was created.
B+-tree will look like this 
```js
[
  { key: { age: 25, name: "Alice" }, value: ObjectId("1") },
  { key: { age: 25, name: "Bob" },   value: ObjectId("2") },
  { key: { age: 30, name: "Charlie" }, value: ObjectId("3") },
  { key: { age: 30, name: "Daniel" }, value: ObjectId("4") },
  { key: { age: 35, name: "Eve" },    value: ObjectId("5") }
]
```
Now we can easily locate users with age = 25, all users with age = 30 and name = "Daniel", all users with age in a range and names sorted. 

The best case is when the query match exactly. like 
```js
	db.users.find({age: 25, name: "Bob"});
```
Here index was used fully thus making it blazing fast. 

Also 
```js
	db.users.find({age: 25});
```
Here, matches first field in index. It is still fast(uses index for `age`, then scans matching names. )

But here Index can't be used as age is missing
```js
	db.users.find({age: "Bob"});
```
It is like skipping the first column of a book index

Like if we imagine we need to know the person with city and name, if we don't know city, then we can't use anything to find them. but if we know city, then we can actually find that person but little bit late. 

In case of sorting,Here also we can't skip age.
```js
	.find().sort({age: 1, name: 1})
	.find({age: 30}) .sort({name: 1}) // supported

	.find().sort({name: 1}) //  not supported as skips age
```
Also order will matter, as MongoDB will always index by the first field, then by second. 

==Always include first field for index to work and order matters==.
sorting is supported if order matches index field. 
###### Text Indexes
Let us search for words or phrases inside string fields in docs, like a search engine
```js
	db.articles.createIndex({ content: "text" })
```

So, there is a reason why we don't use normal index.
let's say we have 
```js
	{ title: "Introuction to MongoDB" }

	db.articles.find({title: "MongoDB"}); // doesn't work
```
Regular indexes only match exact field values, not partial words or text inside strings. To find anything that contains `MongoDB`, we need a text index. 
###### Partial Indexes
###### TTL Indexes

## Data Modeling
#### Embedding vs Referencing
Embedding should be done for tightly coupled data. Embedding has faster reads. It is complex when updating. Can hit 16 MB BSON limit. changes are atomic also. While in multi-document operations need transactions. We need to embed when data is always needed to access together. Data isn't shared. and data rarely changes. and remember that sub documents can't be independently indexed. 

For referencing, one-many or many-many one is preferred. For frequent updates. Slower reads(requires `$lookup`, don't use populate). May be atomicity can't be achieved(need transactions).  
#### Schema Design Patterns
###### Approximation
###### Attribute
###### Bucket
###### Computed
###### Document Versioning
###### Extended Reference
###### Outlier
###### Preallocated
###### Polymorphic
###### Schema Versioning
###### Subset
###### Tree and Graph
###### Aggregation Pipelines Stages
1. `$match`
2. `$project`
3. `$group`
4. `$sort`
5. `$limit`
6. `$skip`
7. `$unwind`
8. `$lookup`
9. `$facet`
10. `$bucket`
11. `$bucketAuto`
12. `$replaceRoot`
13. `$replaceWith`
14. `$graphLookup`(recursive self-joins - like finding tree structures)
15. `$densify`(fill missing time series data automatically)
16. `$setWindowFields`(window functions, like SQL Over() - running totals, ranking)
###### Data processing operators
1. `$sum $avg $min $max $first $last`
2. `$push $addToSet $set $unset $mergeObjects`
##### Array Operators
1. `$arrayElemAt`
2. `$filter`
3. `$map $filter`

###### Performance Optimization
`.explain`, covered queries, aggregation pipeline optimization(stage ordering), `$merge`, Hot vs cold data handling
Understand query plans (IXSCAN, COLLSCAN)
Query Planner deep dive(how mongoDB chooses indexes)
Use of `$hint` to manually force index use
WiredTiger storage engine internals(How Mongodb actually stores on disk)
###### Data Modeling
Embedding vs Referencing, Schema design patterns (Bucket pattern, Outlier pattern) etc.
###### Security Basics
Role based access, Field-level redaction (`$redact`), encrypted fields
###### Admin Stuff
Replication, Sharding
###### Cluster and REplication
Replica set internals, read concern levels, write concern levels
###### Sharding(Horizonal Scalling)
Shard keys, Chunk balancing Mongos vs Config Server roles, Zones(shared key tagging and routing)
###### Failures
Rollback, Oplog replay, Resyncing replicas manualy, Disaster recovery strategies
###### schema Design Patters
Outlier pattern, Bucket Pattern(for time series), Extended Reference Patter(denormalized joins), subset Pattern
###### Security
LDAP Authentication, Kerberos Authentication, x.509 Certificate Authentication

Combine MongoDB with Kafka, S3, Data lakes, Microservices

index cardinality and covered queries. 

- Index intersection?
    
- Index design strategies?

