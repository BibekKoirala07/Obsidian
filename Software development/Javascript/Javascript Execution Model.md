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
### setTimeout
### Js event loop