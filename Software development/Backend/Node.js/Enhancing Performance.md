# Cluster Mode
##### Multiple Instance, One Program
- It is a type to run multiple copy of node that are running single server inside them
- It is tricking node to have multiple threads

##### nodemon doesn't work with clustering properly
When we start clustering in our node application, we are gonna start multiple node processes. There is always going to be one parent process called Cluster Manager which monitoring the health of individual instance of node application that we are gonna launch.
Cluster Manager is responsible for seeing health of each instance. When we start cluster in node program, we start multiple node instance. The Cluster Manager doesn't actually execute any application program. The Cluster Manager isn't responsible for handling incoming and outgoing request or querying the database and other things. Rather each node instance is responsible for that.
![[Pasted image 20240531093505.png]]
#### Normal Node program
 At first node runs the index.js file content and executes it, creates event loop and create a single instance of node
![[Pasted image 20240531094030.png]]

##### Node with clustering
It is same just as normal node program i.e. we will run `node index.js` and node will run the content of the file and create a instance but there is something different. The first instance of node that get launched when we run the command is called "Cluster Manager". Then "Cluster Manager" is then responsible for starting up "Worker Instance" and "Worker Instance" is responsible for handling incoming request. To create "Worker Instance", the "Cluster Manager" is going to require in "Cluster" module in node. There is a special function name "fork()" in "cluster" module. When that function is called then that function reruns the `index.js` or executes `index.js` second time but slightly differently i.e. in second time, it starts our worker instance. it happens for everytime except first time. i.e. second, third, fourth. 

![[Pasted image 20240531094107.png]]
The Cluster Manger has that property called `cluster.isManager` property always sets to `true`.

We also have `cluster.isWorker` to check if the instance is of worker threads. 

### Caveats of node without clustering
When we make artificial delay in any node route, for e.g. `http://localhost:3000` and make another route, lets say `http://localost:3000/fast`  and make browser request in two tabs, first request being `http://localhost:3000`. Despite that `fast` route being simple, it is unable to fetch in time and have to wait for first route to be completed and then only we will get the response. This is the caveat of having only one instance. This problem can be solved by clustering

### Caveats of node with clustering
Don't use that much `cluster.fork()` as there is a limit.
# Worker Threads
