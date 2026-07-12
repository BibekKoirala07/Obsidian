Always take the baby steps. Always have the thorough understanding of the problem. 

Take baby steps. 
Understand the problem statement. 

break down the problem into components(essential)
Note: do not create components for the sake of it. create component that you know are must. 
Like for designing facebook, we need to have auth, notification, feed, gamitication. 

after that dissect each component and let's say we have feed, and it might have - generator, aggregator and web server. go into it first. 

For each sub component, it should have 
- fault-tolerance
- scaling
- async processing(delegation)
- database and caching
- communication

### Week 2 (Relational Database)
Whenever new things comes in computer, it always finds its place in financial domain.When computer were invented, the first program written was accounting program. Just like that when database were created, they were created to solve the problem of financial system. So key properties were
- Data consistency
- Constraints
- Data Durability
- Everything in one place
- Data integrity

There is ACID property. 

Need to see more. 

### Scalling Database

### Sharding and Partition
Sharding is about database while Partition is about data.

minimize the cross shard data. 

### Non relation database 
Most non relational database shard out of the box. 
###### Documents DB 
is good with partial value update. 
###### Graph DB
![[Pasted image 20260511201606.png]]
###### Key Value DB
![[Pasted image 20260511201419.png]]

### Database to pick
Db creator creates a DB thinking one problem very well. Whenever DB is conceptualize, it is conceptualize to solve one problem very well. They pick a small niche. 

Like For Relation DB don't let someone add post if the user isn't available. can't create child without parent existing. but mongo DB can. 

But they don't work if the data resides in two different DB. right ? One huge point of DBMS gone. As non DB don't offer those thing so obviously it would scale.

IN NoSQL, Data is modeled to be shared. which means split across different nodes. (cuz Nosql assumes that data is distributed across nodes. so no cross shard queries)

![[Pasted image 20260513203348.png]]

DynamoDB has a property of storing data in very sharded manner. 

You select the Database in System Design in last day. 

The main question is How to select Data ? 
How much data. 


If the data needs to be correct(extremem correctness), can be stored in single node, needs strong consistency and needs complex queries. 

If can't stored in single node. and can do manual sharding(I will create the API server to connect to the required DB) and have expertise in SQL -> Drop the constrainst and go for SQL

can't stored in single node and simple KV based access. -> MongoDB, DDB

Not stored in single node and No specific requirements -> Go for MongoDB

### Caching
Caches(places where we store data) -> normally RAM(not always. )
saves Network I/O
saves Disk I/O and computation. 

Like profile information, specific line from file or some complex joining. 

Caches are just glorified Hash table

### Populates and Scalling the cache
Lazy cache populating -> just means get from DB -> put it in cache and send it to client. (most popular).
And of course while putting in cache, you always put expiry in it. 

Eager Population. There are two types. 
1. Writes go to both databases and cache in the same request call(while creation, write to both DB and redis)
2. Proactively push to cache(proactively pushing to cache like yt recommendation system shows some videos and yt has already cached that video as most people will hit it. )

Scaling is similar to Regular Database.
Vertical Scaling -> make cache bigger
Horizontal Scaling(Read Replica) -> 
![[Pasted image 20260513211304.png]]
![[Pasted image 20260513211459.png]]

Database Scaling is same as cache scaling. 
### Cache at different levels
The main problem of caching is staleness and invalidation. 
Like While sending money. You send your friend some money but cache may store that you haven't sent it. Like that something can't be stored in cache at all. 

Client side caching -> images, logo, js files (huge performance boost) Like in Amazon prime (last point where you left video)

CDN -> set of server distributed across the world. -> request always goes to nearest CDN server. It is used for caching like images and files. (cache information's that isn't changing). Origin Server isn't used to get it. 

We also have Remote cache. The un intuitive thing is that it can be used by multiple servers.

Database caching (Relational Database) - storing the total number of posts by storing in pre computed way. it is not black magic. 

### Message Brokers and Queues
Most interaction we do on web is synchronous

One asynchronous is spinning up a VM (EC2).
![[Pasted image 20260513213438.png]]
Client tell that he want to spin up AWS EC2 instance. api get's it. it put some flag in Db and tell broker what client want to do. that is what happens. 

Message Queues

video processing in YouTube is classic example of asynchronous message.

There are some features of message brokers. 
Acts as a buffer for a message(consumers can process message as their own pace).
Brokers has some sort of expiration like 14 days(like SQS)
Brokers can reque the messages if not deleted. (What if consumer went down while processing the message ? Every brokers gives you API that you can call after task is completed) But there is classic case here. What if task was processed but while calling API consumer went down ? in that case task will be done twice (This is the philosophy of more than once). Sometimes it happens. Our business logic should handle that. But this is still the edge case. 
![[Pasted image 20260513214844.png]]
Process to gethe subtitle. That above video is S3. 

### Message Streams
The semantics of "Write to one" and "read to many".

The difference to message brokers is that you can have multiple types of consumers which are reading the same message. 

![[Pasted image 20260513215735.png]]
You can see here counter and search both can read it right ? Two kinds of consumer reading same message. 

Message lie in Kafka forever. (Of course you can make configuration but still. )
![[Pasted image 20260513220953.png]]
In MQ, the messages are pulled by consumer as one by one. IN MS, each consumer can loop all the message in broker. And message aren't deleted at all. In fact, you can add new service and it can also loop over all the message present in MS. The same set of messages are iterated by different services. 


![[Pasted image 20260513221222.png]]


Essentials. 
Apache Kafka is the most popular Apache Project. 
In Kafka whenever you are pushing the particular message, you are pushing for a topic. 
Each topic in kafka has a partition. 

Hash function is used to know in which parition does the messages goes. One partition can handle about 300 thousands users. 

Learn about Hash based partition. 
All the message realted to one topic will be handled by its set of partition. 
Within partition messages are ordered. But no gurantees across partition.

![[Pasted image 20260516113814.png]]

### Real Time pub/sub
MS and MQ is pull by consumer. but this is push. 
![[Pasted image 20260516114226.png]]
![[Pasted image 20260516114245.png]]
### Load Balancers
When one service want to communicate to other. It also users LB. Not direct request. ok. 
![[Pasted image 20260516115228.png]]
![[Pasted image 20260516115539.png]]
Hash based routing is important. Hash functions result are sticky(means for same input they give same output). Now what if we hash let's say using IP address, userId or other things or even role ? Then the user will always have routed to same service right ? 
### Circuit Breakers
Prevent Cascading failures(means failure in one service makes failures in second service. )
![[Pasted image 20260516120131.png]]

![[Pasted image 20260516120158.png]]
You see to get feed, we get from trending, which gets from posts and profile and things like that. 

Every machines has a limit the no of TCP connection that its machine can handle. 

the main goal is to make call to other service if and only if that service is healthy. 
![[Pasted image 20260516120605.png]]

![[Pasted image 20260516120758.png]]

![[Pasted image 20260516120927.png]]

![[Pasted image 20260516121049.png]]

### Data Redundancy and Recovery
![[Pasted image 20260516121213.png]]

![[Pasted image 20260516121307.png]]

![[Pasted image 20260516121456.png]]
### Leader Election for Automatic Recovery
![[Pasted image 20260516122007.png]]
There is a orchestra-tor. It is like " hey if one of the machine goes down, then I will spin up server in another machine and will connect to LB ". But what if that orchestrator goes down ? Who will keep and eye on that ? So it keeps on going. So we Leader election is a way. 

So we have Leader Follower Setup
![[Pasted image 20260516122255.png]]
The system is auto recoverable. It is the one example of recursion. 
### Client Server Model
![[Pasted image 20260516123127.png]]

![[Pasted image 20260516124345.png]]

We may think that HTTP is so common but Redis doesn't use it. It has made its own protocol on the top of TCP. A lot of Databases also do this. But that also means you need to have your own client to do that(this is critical to understand)

![[Pasted image 20260516124715.png]]

![[Pasted image 20260516124813.png]]

![[Pasted image 20260516125233.png]]

![[Pasted image 20260516125300.png]]

### Blob Storage
Binary large object(everything that can be represented in binary)

S3 is just network attached infinitely scalable file system. 

Read in S3 are slows. It is not full flegded file system(You need to use aws client to work with S3).

![[Pasted image 20260516130726.png]]
### Bloom Filters
used by recommendation systems. 
It is a approximate Data structure. 

![[Pasted image 20260517112851.png]]
Created by researcher called Bloom.

![[Pasted image 20260517113042.png]]

![[Pasted image 20260517113430.png]]

![[Pasted image 20260517113522.png]]

Bloom filter are extremely efficient. We can't store those huge information in set so We just use Bloom filter to make things easy. So Bloom Filter >> Set. 

![[Pasted image 20260517113714.png]]

![[Pasted image 20260517113930.png]]

![[Pasted image 20260517114056.png]]

### Consistent Hashing
Answers one question ? "Who owns this data ? " and nothing else. 

![[Pasted image 20260517114525.png]]

hash based routing. 
It happens in load balancer. 

The implementation is just 2 array and binary search

### Big Data Processing
### System Design example
Setup Read Replication. and have read operation from read replica having. 
### All the System Design Concepts
###### Client
###### Load Balancer
###### Database
###### Cache
###### Rest API
###### Blob Storage
###### CDN
###### Rate Limiting
[0:39](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=39s) 1. Client-Server Architecture [1:17](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=77s) 2. IP Addresses [1:43](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=103s) 3. Domain Name System (DNS) [2:12](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=132s) 4. Proxy / Reverse proxy [2:54](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=174s) 5. Latency [3:18](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=198s) 6. HTTP / HTTPS [4:13](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=253s) 7. API's [5:06](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=306s&pp=0gcJCTAAlc8ueATH) 8. REST [5:43](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=343s) 9. GraphQL [6:38](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=398s) 10. Database [6:59](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=419s) 11. SQL Vs NoSQL [7:56](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=476s) 12. Vertical scaling [8:28](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=508s) 13. Horizontal scaling [8:49](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=529s) 14. Load balancer [9:36](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=576s) 15. Indexing [10:24](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=624s) 16. Replication [11:26](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=686s) 17. Sharding [12:02](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=722s) 18. Vertical partitioning [12:42](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=762s) 19. Caching [13:22](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=802s) 20. Denormalization [14:20](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=860s) 21. Cap theorem [14:55](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=895s) 22. Blob storage [15:42](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=942s&pp=0gcJCTAAlc8ueATH) Content delivery network (CDN) [16:46](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=1006s) 24. Web sockets [17:24](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=1044s) 25. Web Hooks [18:02](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=1082s) 26. Microservices [18:24](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=1104s) 27. Message Queues [18:57](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=1137s) 28. Rate Limiting [19:33](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=1173s) 29. API Gateway [20:11](https://www.youtube.com/watch?v=s9Qh9fWeOAk&t=1211s&pp=0gcJCTAAlc8ueATH) 30. Idempotency


