There are different kinds of messaging available. Synchronous and Asynchronous messaging. 
# Synchronous messaging
In synchronous messaging, It is all about waiting for the message. The sender waits for the receiver to respond before proceeding
Some of the examples are HTTP Call, gRPC call, Database Query, etc. 
# Asynchronous messaging
Here sender sends the messages and move on. 
There are different things here. Some of them are Message Queues, Event Streams and Pub/Sub System. Like leaving the voicemail so that other can listen later. 
Inside Asynchronous based pattern, We have two different kinds of communication. They are 
### Point to Point (Queue based pattern)
Normally, Point-to-point messaging is used to delegate work or tasks to a specific consumer. Even in Point to Point, we have different things to achieve it. Some of the examples are RabbitMQ, SQS, BullMQ, Celery, Sidekiq, etc

Every queue system has producer(the one who create a job), broker(The one who store the job), consumers(workers or the one who consume the job) and ack/retry(the one who ensure that job is completed if not retries are done) system. 

At first producer creates the job, and it is added to the queue. For storage, queue is used to store the job reliably(if delays have been added). Then consumer picks the job and executes. And acknowledgement is sent by that consumer and If the worker fails, the job may retry or go to a DLQ.

The thing is that system guarantees work will eventually be done and even if workers crash or app restart. 

There are different kinds of queue available. 
##### Message queue
It is a communication channel that transfer discrete messages from producer to consumer in reliable and decoupled way. It is a lowest level channel that moves message reliably. Deliver at least once(means consumers can see a message more than once in rare failure scenarios). 
Some of the examples are RabbitMQ, Amazon SQS and more. 

It may sound like it fulfils all the functionality of WQ and TQ but it is just a foundation of those two. Many examples just add features that push them toward worker and task queue, but the core is reliable message delivery. That is it. 

And question may arise why not just use TQ right instead of passing to MQ right ? And why popular Service like RabbitMQ is used right ? Well for that producer need to know about consumer that "Who will run the job" ? If the consumer is down, the producer fails too. That is the huge problem, It makes producer and consumer tightly coupled and creates scalability issues(what if thousands of job arrives at once). MQ doesn't care who consumer will run the job. It just stores the message. It is just reliable message transport system. MQ Is normally used in decoupling microservice, - **Decoupling microservices** → Producer doesn’t need to know consumers
    
- **Handling spikes** → Jobs are queued, workers pull when they can handle them
    
- **Reliability** → Ensures work isn’t lost if a worker crashes
    
- **Batch processing** → Workers can process messages in batches
    
- **Event-driven systems** → Worker queues and task queues rely on MQ as the backbone

MQ is the simplest and most basic form of asynchronous communication. It is n't concerned with "jobs", "schedules", "retries" or "priorities". All it does is move message reliably from producer -> to one or more consumers. 

It is simple FIFO delivery. 
##### Workers queue
WQ = MQ + Consumers who are workers.

A Worker Queue is simply a Message Queue + Workers that actually do the job.
In MQ, we move message from A -> B
But in WQ, the message actually represent work to be done. and we have dedicated workers whose job is to pull messages and process them. 

Steps:
Producer send a message like "resize image". Queue stores the message reliably. Workers(special consumer processes) pull messages when ready. Worker process the job. Worker acknowledge success. and if it fails message can be retried or re-queued. 

Now the question may arise that why does worker queue even matter ? The thing is it introduces delegation of work. You are no longer sending message but offloading real computation. So offloading work. did you understood. It is like "drop this job into the queue and who ever is free, please pick it up and finish it". You know like Work Delegation. 
You can think queue to be conveyor belt holding pending tasks. The worker pick it as each order and complete it. If one worker fails, the other pick it up. 

In WQ, We add Processing logic, and let the worker process it. Unlike MQ which is for sending the message or delivering a notification("Hey, a new user joined"), WQ isn't about message, it is about job. In Worker Queue, the payload is the job to execute. And the Worker acts like the job to execute.

WQ is built on top of MQ. eg. RabbitMQ Workers, AWS SQS Workers.
##### Tasks queue
it is about job orchestration. the thing is that it add scheduling, retries, priorities, dependencies on top of Worker Queue. 

So, TQ = WQ + intelligence(scheduling, retry, priority, tracking, etc. )

WQ just execute background jobs while TQ manage and orchestrate background jobs. In WQ, Worker processes pull the message and same in TQ as well. In WQ, basic ack/retry exists while TQ contains, Retries, delays, priorities, rate limiting, job tracking and progress events. In TQ, job are often persistence. meaning the data  about them remains whether the job failed, completed or delayed. We use WQ just for job execution but if job management and orchestration is needed then we use TQ.

So the question may arise that can't we do retrying failed job, prioritizing jobs, scheduling jobs and monitoring isn't done in WQ. It can be done but we need to handle them manually. In Task queue, we tell the rules for how jobs should behave. And system already gives us automatic retires, delayed jobs, job state tracking, UI, job dependencies. 

| Layer | Description                                                 |
| ----- | ----------------------------------------------------------- |
| MQ    | Moves data reliably                                         |
| WQ    | Workers pick up messages and do work                        |
| TQ    | Add management(retries, scheduling, monitoring, priorities) |
For real life analogy, Let's say you have kitchen worker. If the worker just cooks what orders comes, then it is WQ while Kitchen manager is TQ he controls what gets cooked, retires failed dishes and what not.  Even in RabbitMQ, we have to manually try retry logic, delays, scheduling, priorities and monitoring. You can't just write 
```js
	queue.add("resizeimage", {fileId}, {delay: 60000, attemps:3 }) // automatically delaying for 1 minutes, retry 3 times and store job state
```
and expect RabbitMQ to do it. It just moves bare message., You can build it but you must design the behavior yourself. 
### Broadcast Pattern(One to Many)
Normally, Broadcast (one-to-many) messaging is used to disseminate information or events to multiple subscribers.  In Broadcast pattern, we have multiple way. Here we have one publisher, many consumers. 
##### Pub/sub queue - "Tell everyone now"
Pub/Sub is the most direct broadcast mechanism. A publisher sends message to a topic or channel. All subscriber to that topic receive a copy.

It is mostly fire and forget type. It is mostly about loose coupling and are often non-persistent(though some broker support persistence). Ordering is also usually not guaranteed.

Some of the examples are Redis pub/sub, google pub/sub, Nats, AWS SNS, etc. It's use cases are Notification, RealTime Updates, Chat broadcasts, Cache invalidation events and things like those. Think Fire-and forget announcements. 

Let's visualize. A user publish a post in social media app. Like "User 123 just posted a photo".  So post service will publish the events like `"user.posted", payload: {userId: 123,postId: 4566}`. and Notification Service and Analytics Service both subscribes. For notification services it could be "Your friend posted". for analytics, it could be increment "posts count". Both service receive it simulataneously.

For standard redis pub/sub:
```js
//publisher
redis.publish("user.posted", JSON.strigify({userId: 123, postId: 456}));

redis.subscribe("user.posted", (message) => {
	const data = ....;
	sendNotification(userId, data.postID);
})
```
Thus no persistence, no queue, just instant broadcast, If subscriber was offline when it was published ? It's gone. Missed forever. 

It is used for real-time updates(chat-rooms, live dashboard, notification, signals, cache invalidation) or things that matter right now. 

It is very bad for historical data processing, critical jobs(if missed, gone forever)
##### Event Stream - "Record everything so anyone can catch up later"
Event Stream = Pub/Sub + Storage + time. Like Events are published to a stream and stored durably so that consumer can replay or process them later in order. So It is both a message delivery system and data log. 

Some of the examples are Apache Kafka, Redpanda, Pulsar, etc. 

Pub/sub isn't re-playable while Event Stream is. 

"Let's record everything that ever happened, so any consumer can catch up later".  

Here we don't broadcast events - we just store them in an ordered log so consumer can read, replay or reprocess whenever they want. Like a CCTV recording, even if we weren't there when it happened, you can rewind and see it later. 

Messages are stored for days or forever. Consumers can reprocess old events. Within a partition, events are ordered. and message are stored on disk, and could be replicated across brokers. 

Pub/Sub is like Loudspeaker while Event Stream is like Security Camera. 
##### Event Bus
It is like internal pub/sub mechanism.

It is normally often used within one app or bounded context. It is usually in memory. The purpose it to decouple component/modules but the boundary is same service

Some examples are Nestjs Event Emitter, Node.js Event Emitter and some custom eventBus patterns. It is used to trigger reactions between modules, decouple internal features

Think "Tell other parts of this app that something happened".