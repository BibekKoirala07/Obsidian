This are complex MongoDB things. MongoDB is basically a whole cloud system pretending to be "just a database".
## Aggregation Pipelines Stages
Aggregation framework is all about building a pipeline of steps that runs on the data that is retrieved from the collection and then gives you the output the form we need. Every stages receives the output of the previous stages.
![[Pasted image 20250616161655.png]]

## Transactions

Basically How to execute multiple operations atomically. A sequence of read and write operations that are treated as single atomic unit. It helps to upheld ACID principles.
It is extremely useful in things related to finance, inventory deduction, order creations etc.

Requirement for using Transactions: Replica set or sharded cluster. And also driver support like Mongoose, native Node.js driver etc.

There are two types of transaction. Single-document(enforced by default in MongoDB) and Multi-document transactions(explicitly started using sessions)

For transactions we need sessions and sessions means all the request are grouped together
logically and behind the scene store in a constant.

```js
// THIS IS FOR DEFAULT NODE.JS DRIVER AND NOT MONGOOSE
const session = db.getMongo().startSession();
session.startTranaction();

await session.abortTransaction();

await session.endSession();
```

The writes allowed per transaction are `insert`, `update` and `delete`. However, `createIndex` isn't allowed.
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
db.users.createIndex({ email: 1 });
```

let's say we have `users` collections that looks like this.

```js
[
  { _id: 1, name: "Alice", email: "a@a.com" },
  { _id: 2, name: "Bob", email: "b@b.com" },
  { _id: 3, name: "Charlie", email: "c@c.com" },
];
```

MongoDB reads every document in `users`. For each document, it extracts the value of `email` field. and `_id` of the document. It build the intermediate structure.

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

The email value is stored in each node. and a pointer to the document's \_id. MongoDB keeps this B-tree sorted for fast binary-style searching.

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
db.users.createIndex({ age: 1, name: 1 });
```

This means first sort/index by `age`(primary sort) and for same `age`, by `name`. "Index by age, and for each age bucket, index by name".

For example

```js
[
  { _id: 1, age: 25, name: "Alice" },
  { _id: 2, age: 25, name: "Bob" },
  { _id: 3, age: 30, name: "Charlie" },
  { _id: 4, age: 30, name: "Daniel" },
  { _id: 5, age: 35, name: "Eve" },
];
```

and let's say compound index was created.
B+-tree will look like this

```js
[
  { key: { age: 25, name: "Alice" }, value: ObjectId("1") },
  { key: { age: 25, name: "Bob" }, value: ObjectId("2") },
  { key: { age: 30, name: "Charlie" }, value: ObjectId("3") },
  { key: { age: 30, name: "Daniel" }, value: ObjectId("4") },
  { key: { age: 35, name: "Eve" }, value: ObjectId("5") },
];
```

Now we can easily locate users with age = 25, all users with age = 30 and name = "Daniel", all users with age in a range and names sorted.

The best case is when the query match exactly. like

```js
db.users.find({ age: 25, name: "Bob" });
```

Here index was used fully thus making it blazing fast.

Also

```js
db.users.find({ age: 25 });
```

Here, matches first field in index. It is still fast(uses index for `age`, then scans matching names. )

But here Index can't be used as age is missing

```js
db.users.find({ age: "Bob" });
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
db.articles.createIndex({ content: "text" });
```

So, there is a reason why we don't use normal index.
let's say we have

```js
{
  title: "Introuction to MongoDB";
}

db.articles.find({ title: "MongoDB" }); // doesn't work
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

When expensive calculations are done frequently and precision of those calculations isn't highest priority. Like in number of likes, number of comments, etc.

###### Attribute

What if some fields can store key-value pairs dynamically as attribute. Instead of creating a fixed schema for every possible property, we store attributes as embedded key-value pairs inside the document. Or when attributes are not known upfront or can vary widely.

###### Bucket

It is about grouping multiple related data points into a single document, rather than storing each one seperately.
Like lets say a sensor measure temp or something every 5 min. Do you really want to create document after documents ? Better make one document with name as `Sensor A` and have reading key with all the attributes in `readings`. That increases readability and reduces writes pressure, and also gain performance in bulk reads.
Like we can also use bucket patters with computed pattern also

```json
	"sensor": "A",
	"minTemp": "",
	"maxTemp": "",
	"readings": []
```

We might see that it is not that intuitive. right ? But it is extremely useful - but only for certain types of problems. But is deeply useful for sensor reading every few seconds, app click stream events, logs every ms, time-series financial prices, IoT data. But for most of our projects, the pattern is over engineered.
==It is just important for analytics events, login data every scheduled time== .

###### Computed

- used to improve read performance by storing pre-calculated values inside docs. It basically involves storing derived or aggregated data inside the docs to avoid expensive computation or aggregation during query time
- the goal is to not use aggregation as much as possible.
- We do it for performance, simpler queries, scalability(reduces load on the database by minimizing computation during read operation)
- The bad thing is we do have some consistency with some trade-offs. Like we need to manage keeping these computed fields up-to-date whenever base data changes.
- We normally use it to manage total likes, number of comments, average rating, total sales and other things.
- We need to run periodic batch jobs or triggers to recalculate the computed fields. And it is ultra useful when real-time consistency is not mandatory.
- We do have data redundancy or data inconsistency if updates aren't handled correctly though. And we do need extra effort to keep computed fields updated.
- Always keep updates atomic to prevent race conditions. and always use transaction to handle inconsistencies.

###### Document Versioning

basically keeping multiple version of document to keep track over time. The things is it can be implemented with separate collections or embedded version arrays.

Like in separate collections we use version reference. like

```json
{
  "_id": ObjectId("..."),
  "title": "Current title",
  "content": "...",
  "version": 5
}
```

and that points to that collection like `post_versions`.

or we can put those version in embedded version also.

```json
{
  "_id": "",
  "versions": [
    {
      "version": 1
    },
    {
      "version": 2
    }
  ]
}
```

We normally use this when app need audit trails or compliance(e.g. finance, legal). Situation where rollback is important and debugging and tracking data lineage is necessary. The main problem with it is storage as MongoDB has 16 MB limit docs size. And we need double the number of writes.

e.g. Wikipedia. current article is latest version and users can also browser or restore previous versions.

###### Extended Reference

It is hybrid approach that combines embedding and referencing. It means we embed some related data inside a document(for fast access) while storing the full detailed data separately in another collection and referencing it by ID.

Like for e.g. We can store `username` and `avatar` for quick display and also include `authorId`.

We use this to enhance speed and simplicity of embedded data for frequently accessed fields. We can get benefit of both world(embedding and referencing) in extended reference. Like balancing performance and consistency.

We also have some downsides. But they are common sense. Like need to maintain sync logic. more complex writes, data duplication, potential inconsistency.

###### Outlier

The Outlier pattern is used when docs has a few large or rarely accessed fields that don't fit well inside the main docs due to large size, accessed rarely or their potential to cause the docs to grow too big. Like lets say we have big bio fields, like biography, a big profile picture, details logs or whatever that is rarely accessed. then we put those in

```
	"bioId": "ObjectId("bioId")"
```

and that `bioId` point to that extra collection which has those things.

###### Preallocated

Nothing that complex, just reserving space in advance to avoid future performance issue when documents grow in size - particularly frequently updated documents.

###### Polymorphic

Just like name says, it is about handling different types of related data in one collection, even if they have different fields. Now image `notifications` collection that may store email, SMS, and push alerts, each with different fields but they belong to same "family" notifications. What to do ? Do we create separate collection for each notification ? like `email`, `SMS`, or other ? or just create `type` ? When should we do it ? When sub types are similar enough to live together, different enough to need difference fields. Or when we need to query all types together. Or We don't want to overhead of managing multiple collections.

The problems is that indexing can get messy and might not apply well across all types. Schema validation gets tricky, structure gets violated and more care is needed.

You can use discriminators to model polymorphic schema properly in mongoose.

###### Schema Versioning

What if schema changes ? Like if at first it was this

```js
	{ _id:  , name: }
```

and later after 6 months becomes this

```js
	{ _id: , name:  , isActive }
```

What to do ? What if existing data in DB still uses the old format ? What to do if we want to support the messaging feature later ? or adding events ?

For that we can use

```js
	"schemaVersion": 1 or 2 or 3
```

We can fetch the document and write the script to change it to version 2.

```js
function loadUser(id) {
  let user = db.users.findOne({ _id: id });
  user = upgradeUserToV2(user);
  return user;
}
```

Let's say we want to update the old format to new format ? We have two choices like Lazy migration(just changing the format when user request the data(not requesting to change as user don't know any schema change)) and write a script.

It is good because it allow safe evolution like adding, removing , updating fields without breaking old docs, debug able and another logical things. But it does add overhead and code complexity and have multiple shapes which means harder indexing. And good things is that is supports backwards compatibility.

The best practice is to include a `schemaVersion` in top-level of document.

###### Subset

Lets say we have too much data in one document. Like we have have `_id username posts(100 arrays)`. It is massive because it contains all the post of users. Now, we may need to show just 3 most recent posts fro profile preview ? why read 100 posts ? We may be pulling too much data into RAM if we pull 100 posts directly and performance for read-heavy use cases also. So we break into two parts, one part with frequently accessed subset and one part with rarely accessed full dataset. So make `latestposts` field and migrate posts to another collection in one way or other.

###### Tree and Graph

The goal of tree pattern is to model hierarchical data(trees), in a way that's efficient for our access pattern.
The common way of doing is to to have `parentId : 1` which may refer to some category or what not. But what if we have four level of documents ?
like

```js
{
  parentId: 1;
}
```

We need to go from child -> parent. so what ?

But we also do in like this

```js
{
  _id: 1,
  name: "Engineering",
  children: [2, 3, 4]
}
```

You can see it is easy to get immediate children and order or

Think of Reddit comment which can be nested in huge amount ?

But what about social network where user follow users, friends of friends, music recommendations, tag-based content discovery or whatever, there graph are used ?

## Failures

Rollback, Oplog replay, Resyncing replicas manualy, Disaster recovery strategies

## Performance

#### Capped Collection

It just means that we can only have certain amount of document in a collection. Means when creating a database, we can say that only this amount of document should be in database. and whenever we try to push the document when the cap is full then it automatically deletes the most previous doc. Like if 3 was cap, we can't push more than 3 docs.

####

## Security Basics

#### RABC

Role based access, Field-level redaction (`$redact`), encrypted fields

## Security

LDAP Authentication, Kerberos Authentication, x.509 Certificate Authentication

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

## Performance Optimization

`.explain`, covered queries, aggregation pipeline optimization(stage ordering), `$merge`, Hot vs cold data handling
Understand query plans (IXSCAN, COLLSCAN)
Query Planner deep dive(how mongoDB chooses indexes)
Use of `$hint` to manually force index use
WiredTiger storage engine internals(How Mongodb actually stores on disk)

###### Admin Stuff

Replication, Sharding

###### Cluster and REplication

Replica set internals, read concern levels, write concern levels

###### Sharding(Horizonal Scalling)

Shard keys, Chunk balancing Mongos vs Config Server roles, Zones(shared key tagging and routing)

Combine MongoDB with Kafka, S3, Data lakes, Microservices

index cardinality and covered queries.

- Index intersection?
- Index design strategies?

These are must-know skills for any developer using MongoDB.

### 1. Data Modeling

- Embedding vs Referencing
- Schema design best practices
- One-to-many, many-to-many in NoSQL
- Handling large arrays (bucket pattern, etc.)

### 2. CRUD Operations

- `insertOne`, `find`, `updateOne`, `deleteOne`
- Query operators (`$in`, `$regex`, `$gte`, etc.)
- Projection, sorting, pagination (`limit`, `skip`)

### 3. Indexing

- Single field, compound indexes
- Index usage via `.explain()`
- Unique and sparse indexes

### 4. Aggregation Framework

- `$match`, `$group`, `$project`, `$sort`, `$limit`
- Pipeline optimization
- Lookup (joins)
- `$facet`, `$bucket`, `$redact`

---

## 🟡 LEVEL 2 — Production-Ready Developer

This level ensures you can work safely in live environments.

### 1. Performance

- Read/write patterns
- Avoiding unbounded queries
- Covered queries
- Query profiling with `db.collection.explain()`

### 2. Schema Versioning & Migrations

- Evolving documents over time
- Using `updateMany`/`bulkWrite` for migrations
- Mongoose or native driver migration strategies

### 3. Security Basics

- Role-Based Access Control (RBAC)
- Field-level redaction (`$redact`)
- Client-side field-level encryption (FLE)

### 4. Transactions (if using replica sets)

- Multi-document transactions
- Use cases & limitations

---

## 🟠 LEVEL 3 — Tech Lead Basics (Infrastructure-Aware)

This is where you move into leadership territory.

### 1. Replication

- How replica sets work
- Oplog, elections, failover
- Read & Write Concerns (`w`, `j`, `readConcern`)
- Read preferences (primary, nearest, etc.)

### 2. Sharding (Horizontal Scaling)

- What is a shard key and how to choose it
- Mongos vs Config servers
- Chunk migration and balancing
- Zones (shard tagging)
- Shard key immutability

### 3. Monitoring and Metrics

- `mongostat`, `mongotop`
- MongoDB Atlas metrics
- Query profiler, slow query logs

### 4. Indexing at Scale

- Wildcard indexes
- Partial indexes
- TTL indexes
- Text and Geospatial indexes

---

## 🔴 LEVEL 4 — Enterprise-Ready MongoDB

This is often DevOps or advanced backend territory — but you should **understand** it.

### 1. Authentication

- LDAP, Kerberos, x.509 Certificate Authentication
- MongoDB internal auth (SCRAM)

### 2. Authorization

- Custom roles
- Built-in roles
- Fine-grained access control per DB/collection

### 3. Backup and Restore

- `mongodump` / `mongorestore`
- `mongoexport` / `mongoimport`
- Point-in-time recovery with replica sets
- Atlas snapshots

### 4. Resilient Architecture

- Multi-region deployments
- Write retries, failover awareness
- Retryable writes and idempotency

---

## 🟣 LEVEL 5 — MongoDB Architect / Tech Lead Mastery

This is **strategic decision-making** and leadership.

### 1. Schema Design at Scale

- Workload-based modeling
- Hybrid models (embedding + referencing)
- Pre-aggregated and computed fields
- Event-sourced schemas

### 2. Cost Optimization (esp. Atlas)

- Cold vs hot storage
- Index cost
- Query cost & tuning
- Data tiering strategies

### 3. Tradeoffs and Patterns

- CAP theorem in MongoDB context
- Denormalization vs consistency
- Write-heavy vs read-heavy strategies
- Eventual vs strong consistency

### 4. Team Leadership Skills

- Reviewing schema and query performance in PRs
- Educating team on anti-patterns
- Setting up proper staging/prod environments
- Creating MongoDB usage guidelines/playbooks

---

## ✅ How to Use This

You can think of it like this:

| Level | Role Target           | Focus                            |
| ----- | --------------------- | -------------------------------- |
| 1     | Junior/Mid Dev        | Fundamentals                     |
| 2     | Senior Dev            | Production safety & performance  |
| 3     | Tech Lead             | Architecture + cluster awareness |
| 4     | Lead / DevOps         | Security, reliability            |
| 5     | Principal / Architect | Strategic leadership             |
