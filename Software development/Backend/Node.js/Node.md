Node.js has collection of  dependencies to execute our code

- `Crypto` and all `fs` modules uses threadpool while `http` uses OS async features

![[Pasted image 20240525122146.png]]
 V8 - is  an open source js engine created by Google and it's purpose is to run the js code outside of browser.
 Libuv - is a C++ open source project that gives access to operating systems underlying file system and networking also.
![[Pasted image 20240525122822.png]]

Node gives us interface which accept javascript and relates to C++ code running in computer.
It also gives us series of wrapper and consistent API for us to use inside of our project.
![[Pasted image 20240525123146.png]]

In the official node.js github
![[Pasted image 20240525123536.png]]
 lib - contains js definition of function and modules that are required in our project. or we can say it is js world of Node.js
 src - C++ implementation of those functions and modules. It is a place where Node.js pull libuv and V8 project and do all those implementation like `fs`, `http` modules

when looking at crypto pbkdf2 function
![[Pasted image 20240525124543.png]]

 Node Event 

 Whenever program in started, then a thread in created and whole code is executed in that thread 
![[Pasted image 20240525134308.png]] Inside of thread, there is event loop. Event loop is just a control structure that decides what that thread should be doing at given certain time. Every program we run has exactly one event loop.

 Whenever we first invoke node code, the event loop don't get executed eventually. instead all the code/content of file is executed and code such as function call to `fs` module, SetTimeOut is automatically detected even at initial invokation and after the content of node.js is executed, we immediately enter the ndoe.js event loop. The event loop is going to executed again and again just like while loop. Every single time event loop runs is called `"tick"`.  Every single time event loop is to be executed, node does quick check should event loop proceed for another iteration.

 in the quick check, node.js does 3 quick checks . The  check are  
 1. Is there any function that is left to be executed. Like there may be function with `setTimeOut(), setInterval(), setImmediate().` or can be written as pendingTimers

 2. The second check is that is there any pending operating system task like http server listening to requests on some port or like to  check if any servers are listening for PORT. "PendinOSTasks"
 3. Check if there are long running operation are still being processed like if there is `fs` module being running. "PendingOperations"


 After the quick check is done then we enter new tick. In new tick, following things happens
 1. node looks at pendingTimers but only include setTimeout() and setInterval() and sees if any functions are passed to those pendingTimers. If the time has expired, then, node call relevant callbacks function
 2. then node looks at pending OS tasks and pending operation, then node calls relevant callbacks. example would be server is listening at port or if some file operations have been completed

 3. Node pauses execution temporarily. Here node just sits here and waits for some events to occurs. Only continue when new pendingOsTask, pendingOperation is done and pendingTimers is about to complete. So we sit around and waits for stuff like file reading, completing timers and other things to happen
 4. Again node at pending Timers. But node here only care about setImmediate().
 5. Handle any close events. Like in readStream code. node runs that callback. It is about cleaning code.
```
readStream.on("close", () => {
	console.log("cleanup)
})
```


 Important. 
 1. We don't running event loop as fast as possible. In step 3 we wait around

 (pendingOperations) We always see Node being said to be single Threaded. but it isn't totally right. Some of function included inside node run outside event loop and outside of single threaded. Only event loop uses single thread![[Pasted image 20240525150710.png]]

 just look at this code
```
const crypto = require("crypto");
const startTime = Date.now();
console.log("sttartTime", startTime);
crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
  console.log("1:", Date.now() - startTime);
});
crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
  console.log("2:", Date.now() - startTime);
});
```
the result of this code is 
```
sttartTime 1716629759819
1: 1034
2: 1035
```
 Since we see node.js is not actually totally single Threaded as `crypto` passed all work to be done by C++. For Some standard library function calls, node decides to not use event loop but rather use threadPool. The thread pool is the collection of 4 threads that can be used to running computationally intensive tasks. But default libuv creates 4 threads in this thread pool
![[Pasted image 20240525160335.png]]
 If event sloop was doing that above task, then node will not able to do anything when it is functioning and those 2 tasks would had require 2 time each but it isn't true. Many of the node features uses threadpool instead of event loop. By using threadpool, we don't have to wait and can use event loop for other functions

 This code justify the presence of thread pool

```
const crypto = require("crypto");
const startTime = Date.now();
console.log("sttartTime", startTime);

crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
  console.log("1:", Date.now() - startTime);
});

crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
 console.log("2:", Date.now() - startTime);

});

crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
  console.log("3:", Date.now() - startTime);
});

crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
  console.log("4:", Date.now() - startTime);
});

crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
  console.log("5:", Date.now() - startTime);
});
```
 the results for my computer are
```
sttartTime 1716632895328
2: 1140
4: 1250
3: 1255
1: 1310
5: 2015
```

 the results of 2015 Mac book pro is 
```
1. 2000
2. 2000
3. 2000
4. 2000
5. 3000
```  
the architecture is
![[Pasted image 20240525162444.png]]
How threads get divided 
![[Pasted image 20240525163240.png]]
f-1 and f-2 get assigned to Core#1 while f-3 and f-4 get assigned to Core#2. It is because the computer has multithreading or hyperthreading enabled. otherwise f-1 would get assigned to Core#1 and so happens to f-2. Each pbkdf2 function require 1s to complete. However all require nearly 2s to complete as hyperthreading doesn't reduce the amount of time required to complete the process. It would allow only some concurrency so that user can believe all 4 process are happening at once but this isn't true. and last one require just 1s

 However we can limit the size of threads
```
process.env.UV_THREADPOOL_SIZE = 2
```



###### 
![[Pasted image 20240525164450.png]]

 (pendingOSTasks)
 We will realize that some function in node.js library doesn't use same kind of thread pool.

```
const https = require("https");
const start = Date.now();
function doRequest() {
  https
    .request(`https://www.google.com`, (res) => {

      res.on("data", () => {});

      res.on("end", () => {

        console.log(Date.now() - start);

      });

    })

    .end();
}

doRequest();
doRequest();
doRequest();
doRequest();
doRequest();
doRequest();
doRequest();
doRequest();

// Output 
812
818
857
885
886
888
944
945
```
 We realize that all request are done simultaneously. Why ? Just as the node standard library has some functions  that make of libuv threadpool, it also has some function that make use of code that is build in  underlying Operating system through libuv. Libuv realize that we are trying to make http request. Neither libuv nor node has any code to handle super low level operation that are involved with network request. Instead, libuv delegates the request-making to OS. So, it is our OS that is doing real http request. Libuv is used to issue the request and then it just waits for OS to emit some kinds of response. So libuv is delegating the work to OS. OS itself decides whether to make new thread or handle the entire process of making the request. Since OS is making the request, there is no blocking of event loop and any thread pool isn't used at all and all work is being handled by OS itself![[Pasted image 20240525172417.png]]
![[Pasted image 20240525173055.png]]In event loop, all this code goes to pendingOsTasks. As long as there is task in that array, node will continue to run


 Crazy Node Behavour

```
const crypto = require("crypto");
const fs = require("fs");
const https = require("https");

const start = Date.now();

function doRequest() {
  https
    .request(`https://www.google.com`, (res) => {
      res.on("data", () => {});
      res.on("end", () => {
       console.log("HTTPS: ", Date.now() - start);
      });
    })
    .end();
}

doRequest();

fs.readFile("multiple.js", "utf8", () => {
  console.log("FS: ", Date.now() - start);
});

function doHash() {
  crypto.pbkdf2("a", "b", 100000, 512, "sha512", () => {
    console.log("Hash: ", Date.now() - start);
  });
}

doHash();
doHash();
doHash();
doHash();

// Output 
HTTPS:  639
Hash:  1245
FS:  1248
Hash:  1340
Hash:  1439
Hash:  1442
```

![[Pasted image 20240525190734.png]]

![[Pasted image 20240525190803.png]]

![[Pasted image 20240525190830.png]]
 The output always same with `fs` module in 3rd. Why ?


![[Pasted image 20240525190226.png]]

 Summary
![[Pasted image 20240525175126.png]]