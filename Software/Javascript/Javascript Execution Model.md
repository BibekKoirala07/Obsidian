### Call Stack
It is a place where js keeps track of what function is currently being runned or where to return when it is done. "who called who".

```js
function three() {
  console.log("three");
}

function two() {
  three();
  console.log("two");
}

function one() {
  two();
  console.log("one");
}

one();

```
Here , first `one` is pushed to stack and `one` calls `two` and `two` is pushed. After that `three` is called from `two`. After `three` logs, it is popped, then `two` is popped and then `one` is popped. 
### Call backs, promises and async/await
### synchronous and Asynchronous
### microtask vs macrotask queue
microtasks in fetch, Promise(VIP line)
macrotasks is setTimeout(regular queue)

Only after microtasks queue is done, macrotasks runs. 
### setTimeout
### Js event loop
Js itself is always single threaded(means only one call stack). 

Inside V8, 
![[Pasted image 20260217114140.png]]

![[Pasted image 20260217114046.png]]
Chrome V8 engine actually dont' have setTimout, SetInterval at all. They are provided extra by browsers actually. 

Web API are extra things that browser provide. 

![[Pasted image 20260217114257.png]]Js is a single threaded language(runtime) which means it has single call stack. It means it can one thing at a time. 

There is no strict definition of blocking or non blocking. It is just a code that is slow. 

Concurrency & Event Loop
One thing at a time, except not really.

The things is Js(runtime) can only one thing at a time. Like you can't do setTimeout and other things when it is doing other code. 

But in browser, we can do it is browser is more than just a runtime. ==Js runtime can do one thing at a time==. 

For browser
![[Pasted image 20260217115631.png]]

But for Backend the WebAPI is C++ api. and threading is being hidden from us by C++. 

The code for running the AJAX request doesn't live in Js runtime but in the browser as a web API. 

When Stack has something, rendering UI is blocked. 

![[Pasted image 20260217141756.png]]


==async is handled by OS in node.js. libuv only helps with I/O, not CPU==

You can create more JS engines running in parallel. Each worker has its own call stack, own event loop, its own v8 instance, and its own memory. 

```
Main Thread  → Handles users
Worker 1     → Heavy task
Worker 2     → Heavy task
Worker 3     → Heavy task
```
And Workers don't share memory by default at all. 

👉 Why most companies use **Cluster + Load Balancer** instead of workers ?


Web page have a thing that we tend to call the main thread. We call it main thread cuz. load of things happens there. It is where rendering happens. It is where the DOM lives. What if the main thread is occupied for long time ? It blocks rendering and it is visible. 

Although we have this main thread, we tends to spawn a lot of series of thread for tasks like networking, encoding and decoding, crypto. But once those threads have done the things that page needs to hear about, it have to communicate(tell) the main thread that task is finished. And it is done by Event loop. 


First part of event loop is Task queue. It is oldest part of EL also. 

![[Pasted image 20260217143123.png]]

