In Mono Lithic server we have.
![[Pasted image 20250619153931.png]]
A monolith contains all the routing middlewares, business logic, database access to implement all the features of our app. But a single microservice contains routing, middlewares, business logic and database access for ==one features== of our app.

Below is the figures of micro services. In monolithic repo we would have single code base, but not in micro services. Here we have software divided into different service with each team solving different problems. Like What if Service B, C and D fails. ? Well Service A does work. 
![[Pasted image 20241213203845.png]]The biggest problem in microservices  is data management. There are some conditions for microservice to functions:
1. Every service gets its own database. or Database per service. Who know devs at one service may decide to change the schema of other service and other devs will be unknown. There is conflict and will disturb both service. In another case, may be different database are good for different service like one service may be good for NOSQL and other for SQL. If we use only one database, then that database will have huge load which isn't desirable at all. ![[Pasted image 20250619161700.png]]
2. Under no condition, One service will use the database of another service. ![[Pasted image 20250619160953.png]]rather just use service of another microservice for easy access of data. Why ? There are various reason.
#### Big Problems with Data
So why do we have problem ? Lets build the basic ecommerce app which has features to Sign Up, List Product and Buy Products. ![[Pasted image 20250619162851.png]]
How do we achieve it in monolith ? In Monolith we got one codebase and one database to service that. ![[Pasted image 20250619163058.png]]
So, We do as shown above but what if we want the code to show products ordered by a particular users ? Well we go to user collection, orders and products collections and get those right ? As we need information from all the collection but still no issues. 
![[Pasted image 20250619163034.png]]
So what do we do in microservice ? We need three services with their own database. ![[Pasted image 20250619163422.png]]
Now what if we want to add in new feature. Let's say we want to add code to show orders made by a particular user. In monolith style, We would reach out to DB for Service A, B and C and make it work but it isn't monolith. As we already know Service from one microservice can't access DB from another service. Like shown below.
![[Pasted image 20250619163638.png]]
This is big big problem in microservice and most of the microservice is possible only if we solve this problem. 

#### Sync communication between Services.
The main gist is to solve data management. There is two ways to communicate between service. ==Despite they seems to be like js syntax. it isn't that at all. So remember this== They are:
###### ==Async (Preferred)== 
 Services communicate with each other using events. There is event bus that handles all the event that is emitted by any service, Let's Say, Service D want to get all the product available for some user. For that we need to use all Service A, B and C. For that Service D will send the event name UserQuery to event Bus and it will forward to A, That Service will pass the event to UserQueryResult to event bus and sent to Service D. then then event will go to B and will get back from that service and again to C and D So if you look just below in Sync Communication, it have same disadvantages. So it is used less that another one. ![[Pasted image 20241213215845.png]] Rather the way is to change the introduce the database for Service D as Database D and as soon as some required Event is triggered in Service A, B and C, modify the database D and Service D can fetch without problem. However, the problem is data duplication and harder to understand. However data duplication isn't problem is that AWS, Azure or GCS isn't that expensive.![[Pasted image 20241213223419.png]]
###### Sync
Services communicate with each other using direct requests. Doesn't have to be http or request that exchange json. Just direct request. type can be anything. Here you can see, we use the service of other and didn't directly communicated over database of others. There are certain advantages and disadvantages here. Here Service D don't need a new database. So that is great. It is easy to understand. However, it is easy to get lost in this kind of maze. We don't know the structure of maze. Let's say we just put request in Service D, it goes to Service A, who know it may go to another service. So, there is maze and it takes all things to work out properly for that request to work properly. What if one request fails ? There is interdependency between two things.![[Pasted image 20241213212721.png]] 
After this I did project which have post and comment. We just did it to understand microservices. We have post and comment as different service. Each post has multiple comments. so while fetching comments, we would do different fetching for each post, that would be excessive fetching. The goal in that program was to fetch post with all the comments.. This would be easy in monolithic architecture but it isn't easy in microservices. For that we will introduce extra Service named Query which have different database. So, we will trigger the event when post and comment are created and will put the required data in Query database and data will be fetched from there. It seems confusing but look at the below picture to understand.
![[Pasted image 20241214211410.png]]
It does creates some problem so below are confusing questions.
Some confusing questions about Microservices![[Pasted image 20241214202951.png]]
Here are some features about event bus . They receive the event and perform the required action which is to do something in other service. event means anything. It could be json, some string, some bytes, etc. For production grade event bus we need to use open source event bus like Kafka, RabbitMQ, something. however, we will do event but which is express app to understand how it works.
![[Pasted image 20241214211653.png]]

### In the express app that I built as event bus
It that express app which I made to understand event bus, we made post, comment, query and moderation microservices. And there is event bus also. First when we create post, it goes to post MS and it sent the event to event bus and that event bus distribute that event to every MS. every MS have event getting features in `/event` route and look for type in event. If type is required one, then perform the event and again emit the event. In our app, all the data at last goes to query so that react can fetch things easily. So what happens is when we create post, it emit the event and goes to Event bus and event bus make sure all MS are sent that event. Whoever need to consume that event (query MS) will consumed. When comment is made, it also goes to EB and it emit the event to everyone. In moderation there is facility to check whether comment is appropriate or not. After it is complete again event is emitted and EB sent that event to comments MS, there the comment is updated and again the event is sent to EB, then to query and thus it is how it is done.

### What are the best way to combat the issue of of MS being down ? 
Like some MS like query may be introduced later. Or some MS may be down. then what ? Then we need to store that in Event Bus data Store. 
![[Pasted image 20241215155227.png]]

### With Docker
put every service in docker one by one and then only use it.
### With Kubernetes
Tools for running a bunch of container.
 
### Architecture of Microservices.
![[Pasted image 20241224112554.png]]
###### New project to do learn architecture.
![[Pasted image 20241224112903.png]]
The project that we are going to make 
![[Pasted image 20241224124252.png]]
Following are the events ![[Pasted image 20241224124620.png]]The overview of the project.
![[Pasted image 20241224124801.png]]
