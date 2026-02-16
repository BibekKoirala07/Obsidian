Concurrency just means ability of system to manage multiple tasks by interleaving their execution which creates a illusion of simultaneity. It is effective for I/O Bound operation. It just means dealing with multiple tasks at the same time logically, not necessarily physically at the exact same instant. 

There are mainly two kinds of tasks
One of them is I/O Bound tasks, another one is CPU bound tasks. 

There are many types of Concurrency. 
### Thread based | Single Process, Multiple threads
The core idea of Thread based concurrency is that our server run one process and it spawns multiple threads on the basis of request. And those each threads can run independently, including blocking I/O. One problem is the threads share the same memory space, which makes data sharing easy but requires careful synchronization. 

```java
1 OS process
  ├── Thread 1 → handles requests / tasks
  ├── Thread 2 → handles requests / tasks
  ├── Thread 3 → handles requests / tasks
  └── ... up to N threads
```

To fully understand let's simulate with synchronous function. 

Let's say we have this function
```python
def handle_request(request):
	result = db.query()
	return result
```
So lets say request A comes in. Thread 1 will pick that and will run from top - to -bottom. Another request B comes in and Thread 2 will pick that request(the underlying concept is that threads can run in parallel because they aren't the same thread). Blocking I/O doesn't block other threads.  
```python
Time 0s: Request A → Thread 1 starts → DB call
Time 0s: Request B → Thread 2 starts → DB call
Time 0s: Request C → Thread 3 starts → DB call

Time 3s: Thread 1 → DB finishes → response sent
Time 3s: Thread 2 → DB finishes → response sent
Time 3s: Thread 3 → DB finishes → response sent
```
But doing this we are achieving parallelism for blocking task. And each thread can wait for I/O independently. Many CPU Bounds tasks can run simultaneously which means multiple threads can compute heavy tasks at the same time on multiple cores. It sounds familiar and is used by Apache with threads, Django WSGI with threaded workers.

But it has its own downsides. What if there are more request ? Each threads has its own stack(which is 1 MB in many OS). OS switches between threads -> expensive if threads are numerous. It will have lots of race condition/synchronization needed. and what about scalability ? Do you think you will create a thousands of concurrent connection/threads is efficient. One of the thing is here, race condition must be avoided. We must synchronize shared memory. 

One question is if single process, single thread looks/sounds better, then why do enterprise choose this thread based concurrency model ? 
Cuz it looks familiar. It makes sense. Back then there was no concept of async at all. Tooling was weak, debugging async was pain in the ass. Developers standardized this model early. enterprises built millions of lines of code on top of it. Companies don't rewrite easily. There is lot of blocking code in enterprise as they are old. Real enterprises have lot of synchronous drivers, ERP systems, file systems, which most of them are blocking and it sounds good at that time as it was predictable, was debug gable, supportable. Async just make debugging difficult, complex stack traces, subtle bugs, steeper learning curve. Hardware is cheap, engineer are expensive. Instead of rewriting in async, just add 5 more machines.  So it is just common sense that banks, government and hospitals choose Java. And guess what most enterprise servers don't create infinite thread. They use pools. Like this
```
Max threads = 200
Queue = 1000 # means that after 1200 request, it will backpressure. 
```
This creates predictable memory, stability and back pressure(means when system is overlaoded, it slow down or pushed back on incoming work instead of crashing. i.e. "I'm full right now. Send less"). This avoids the "thread explosion" problem. And in enterprise, somebody will always write blocking code. Now imagine when that peice code runs, entire server freezes(you don't want that).

However more modern companies are using async more. Why ? cuz async nowadays is cloud native, there are async DB drivers. There is strong dev ops reason for async also. 

So, in Summary. ==One request = one thread==. 
### Event-loop based | Single Process, Single thread
Our Server run one process and inside it, one thread handles all the requests. Each request is handled top - to - bottom sequentially. 

So let's say we have this function
```python
def handle_request(request):
	result = db.query("Some db query"); # It is blocking query
	return result
```

While previous request hit the db query, it waits for database to respond. During this time, no other request can be processed by this thread. Only after db.query finished and previous server respond, the thread can start handling the next request.

Internally it looks like this
```python
While True:
	request = accept_new_request()
	handle_request(request) # you see, it must be finished before you can run new request again. 
```

Let's simulate slow DB call. 
Suppose:
```python
def handle_request():
	time.sleep(3)
```
And the order looks like this. 
```python
Time 0s:  Request A arrives → starts
Time 1s:  Request B arrives → waits
Time 2s:  Request C arrives → waits
Time 3s:  A finishes → B starts
Time 6s:  B finishes → C starts
```
Waiting request usually sit in web server queue(Uvicorn, Gunicorn keep pending requests). OS also holds incoming connection. If that queue is full, you get 
```python
	503 Service Unavailable or timeout
```

==Why is this bad ? ==
Le't say DB call takes 500 ms and You get 100 users at once. Then ? total will take 50(500 ms * 100) seconds to respond. 

This model is easy to reason, No race condition and terrible in modern world. Nowadays API call include, File reads, redis, dB call and what not. So it kills performance. 

Here enter async abilities. 
With async 
```python
async def handle_request():
	await db.query()
```
Even now we have single process, single thread. But when the code hits `await`. The thread is freed. Next request runs.

One thing to remember is ==that nothing is truly forced to stop==. When two request(previous and next) request are running and previous request is in the time of doing it's DB query(I/O bound tasks) and next request is running. And when previous request get's its DB query back, it doesn't directly run its code in thread. It have to wait for its turn. It will get its turn when next request hits `await` and previous request is in next line in Event loop. 

and also sometimes we may think that in terms of request of more than two requests. When that next request hits the `await` in its path. Then you may think that previous request gets the thread ? But that is not right. After that next request hits the `await`. The request which was first free(not previous request, possibly may be but still) will get the thread. 

When python sees `await`. It is saying 
```python
Pause this function and let another tasks in Event loop run
```

When it is encountered, Current request is suspended, It's local variables are saved and control goes back to event loop. 

Let's see it with example. 
3 users `A B and C` hit the endpoint. All run this function
```python
async def endpoint()
	await db.query()
	return "ok"
```
Step 1: Start
```python
A starts
A hits await → paused 

# only after A is paused, B starts
B starts
B hits await → paused

C starts
C hits await → paused
```
Now all are waiting for DB. And Thread is free. (One thing is that even though they looks that they started at same time, it is not true as it is not possible to start at same time due to single thread availability but time duration is so small that we consider it that it start at same time. ) and DB call is working in parallel(normally) and not fastapi as it is not possible. 

Step 2: Db responds to B first
Db finished B first. Event loop sees that B is ready and resumes B and B return response. A and C still waiting

Step 3: DB responds to A
Resumes A and A returns

 Step 4: DB responds to C
Resume C and C returns 

One thing to remember is that nobody is forcibly paused. Only when the code hits `await` does it pause itself. 

If the code doesn't await -> it blocks. In async, CPU is always busy. While one request waits for something, it(thread) can handle other requests(tasks, co-routines). 

Coroutines -> A unit of async execution. It is normally a function that pause and resume. 
```python
async def handle():
    await db.query()
    await http_call()
```
this function is co routine function. 

Task = A scheduled co routine

==Two tasks never finish at a same time==. It is just not possible. 

In Fastapi, unless we add something,  ==we have One process.  One thread + One Event Loop handling many tasks(requests)===
```python
1 OS process
  └── 1 main thread
        └── 1 asyncio event loop
              └── many async tasks (requests)
```

And the things we discussed above is great for I/O bound work but what about CPU-bound tasks (which means synchronous, high compute, intensive tasks )? Cuz it blocks everything right ?As fastapi don't see `await`. It will continuously run those. Like video encoding, hashing, complex loops, image processing(resizing, filtering). Since these tasks never encounter `await`. So they block the event loop in a single-threaded async server. 

And Before moving to multiple process, we need to understand about cores in computer. As we know we can get the illusion of parallelism in single core or any amount of core. Things don't happen at parallel. But at real detail, OS schedules the thread on the core. And that same core(if it is single core), OS timeshares cores between VS Code, Browser, file explore and shits like that. Even on a single core machine, the OS gives each program tiny slices of time. The illusion of parallelism comes from **rapid context switching**. We can even run multiple fastapi process in single core but throughput may be little bit less as all of them share a single core. ==One core = One process at a time==.

Each process is tied to the core. If you have 8 cores, you device can run 8 processes truly in parallel. Same for guvicorn process(workers).

And Fastapi often run multiple processes(workers)
Let's say there are 4 processes. What will happen if we do that ? well process corruption might not stop our entire server. If one process(thread) can serve 100 request, then 4 process/thread can do 400 request. So it is awesome. And they all have independent event loop. 

==Now the biggest question is why does FastAPI and Node.js prefers async(one process, one thread, one event loop) over One process, many thread==. 
Cuz Web API's mostly are I/O bounds. async lets single thread handle thousands of request efficiently. Since One threads can handle 1000+ requests; creating many threads would be just inefficient and heavy. It helps in predictable scaling and makes it easy to reason about concurrency. And if you need CPU bounds scaling -> add workers. And FastAPI is built on Starlette + ASGI which is async first by design. Context switching inside thread is cheap. It is memory efficient. It don't need to manage hundreds of threads. Just because new request arrive, async don't make new thread at all. The thread is never idle even with thousands of waiting requests. 

One process, many threads is classic multi-threaded server, memory heavy and less scalable. It may be necessary for blocking tasks like accessing sync DB and sync file. It is also easy to reason about as threads are independent. It works well for blocking code. But threads consume memory(stack space per thread). Context switching is nightmare and expensive if there are hundreds of stacks. Does it scale for thousand of concurrent requests ? This is exactly how traditional web server like Apache with threads or Django with WSGI + threaded worker work.  
###### Advantages
###### Problem
###### Confusion
I might think that `async` means single thread. No it doesn't
It just means `While I am running blocking code, I will free the thread and that could be used for other tasks`. It may sound like async means single thread as it is normally associated with single process, single thread model but it is not true. We can have multiple threads and also `aysnc`. In fact, most of the production nowadays use this settings. For example like We have have `worker_threads` in node and use async at both process and threads. `async ` just means co operative multi tasking using a event loop. It usually is implemented on a single main thread, but it can be combined with thread pools or multiple event loops. 
### Process based | Multiple pre-forked processes(1 request per worker)
The key idea is the server starts many processes in advance, and each process handle one request at a time. No event loop. No async scheduling, no shared execution. 

Plain and simple. ==Each request = one worker process==.
```
Master Process
   ├── Worker Process 1 → handles Request A
   ├── Worker Process 2 → handles Request B
   ├── Worker Process 3 → handles Request C
   └── Worker Process N
```
At startup, server creates a workers, and it start 
```
Start 20 worker processes
Wait for requests
```
and when the request arrive
```
Request A → Worker 1
Request B → Worker 2
Request C → Worker 3
```
And when there is blocking code, it only block itself, other workers can continue. No `await` exists at all. This medium is very safe. Crash, Memory leak and infinite loop are isolated and if it happens, only that worker is affected. 

Is is very simple. It provides excellent isolation. Buys don't spread. Works with any blocking code. Easy debugging. 

However the negatives are that, It consumes heavy memory, Each process have heap, stack, libraries and interpreter, 
```
1 PHP worker ≈ 30–50 MB
100 workers ≈ 3–5 GB RAM
```
Forking process is expensive. Wasted resources. 

And for the caution that process count don't spikes. PHP has 
```
MAX_PROCESS_POOL = something(let's say 50) # crossing this will create backpressure
```

PHP Ecosystem is the poster child of this model. We also have Python WSGI Servers in Process Mode. Ruby on Rails also use this. Low level system(C/C++) often use this pre fork one. Almost all cheap/shared hosting uses process model. 
### Co-routine based | Co operative multitasking/co-routines
### Actor Based 
### Distributed consensus
### Scheduling algorithms
### Queue internals
### How to scales with cores
CPU has cores(Each core can run one thing at a time). Modern CPU have multiple cores. 

Each core can run one thread at a time(roughly). So
```
1 core  → ~1 real execution unit
4 cores → ~4 parallel executions
8 cores → ~8 parallel executions
```
Our goal becomes to `Keep all cores busy - but not overloaded`. Idle cores means wasted money and overloaded cores means slow system. 

==One thing to memorize is that threads share the process's memory but can run independently==. 

So 1 busy process(or main thread) = 1 core

We have to realize that one process can't use all cores at all. Most run times have 
```
1 processes -> 1 main thread
```
Suppose Node.js (one event loop), Python(asyncio), Fastapi async.
```
8-core machine
1 Node process
→ uses ~1 core
→ 7 cores idle ❌
```
Since 7 cores are idle, that is wasted power, So we must add processes. 

Scaling with process is the most common way. 
In that situation, we must run multiple worker processes. 
```
8 cores

→ 8 processes
→ each on 1 core
```

And 
```
Load Balancer
   ↓
Process 1 (Core 1)
Process 2 (Core 2)
Process 3 (Core 3)
...
Process 8 (Core 8)

```
### Intuition that I gain
###### Final simplified truth

Remember this:

> Threads = parallelism  
> Async = concurrency

Parallelism → doing things at the same time  
Concurrency → managing many things at once

Async gives concurrency.  
Threads give parallelism.  
Good systems use both.

---

If you want, next we can connect this to **how modern web servers (Nginx → App → DB) combine all models together**, which is pure system-design gold.

In real world, they combine, processes with threads, event loops and queues. Why ? cuz each model solves a different problem. 
Normally modern architecture looks like this
```
Client
   ↓
Load Balancer / Reverse Proxy
   ↓
Multiple App Processes
   ↓
Each Process:
   └── Event Loop (async)
         └── Thread Pool (CPU / blocking work)
```
Why do we start with multiple processes ? One main reason is Fault isolation + CPU usage. If we run single processes with single event loop, then when something faulty occurs, everything is down. With multiple processes, this can't happen. And normally each processes uses async with itself. Like this
```
Process
  → 1 thread
     → event loop
        → 10k connections
```
Because async massive I/O, uses very little memory and avoid thread explosion. So each process can handle thousands of clients. But one thing to keep. async breaks on CPU bounds tasks. So systems add `Thread pool` for tasks like Password hashing, PDF generation, Image resize, legacy DB driver. 

Flow
```
Request
  → async handler
     → offload to thread pool
         → heavy work
     → back to event loop
```

And another things is background workers like email, reports, ml jobs should not run in web server, They should be separate processes. Other wise slow jobs will kill API latency. 

Now since we have many processes in many machines. We need traffic distribution. Here we need load balancers .

Here is what full production stack looks like in Uber/Netflix
```
User
 ↓
CDN
 ↓
Load Balancer
 ↓
Nginx
 ↓
App Cluster (Kubernetes)
   ├── Pod 1 → 4 processes → async + pool
   ├── Pod 2 → 4 processes → async + pool
   └── Pod 3 → 4 processes → async + pool
 ↓
Redis / DB / Queue
 ↓
Worker Cluster
```

One thing to remember is that no serious system uses `just async` or `just threads`. They use
- async for I/o
- Threads for blocking code
- processes for isolation

In Summary,
No system just uses one model. They try to get benefit of most system. 