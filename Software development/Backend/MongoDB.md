This are complex MongoDB things
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
###### Indexing
Single field indexes, Compound indexes, Text indexes, Partial indexes, TTL indexes
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

