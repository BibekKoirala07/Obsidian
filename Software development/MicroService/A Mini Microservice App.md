Sync Communication. directly. 
Async Communication with event broker. 

Event bus
![[Pasted image 20250715143629.png]]

Dealing with missing events
We store the events if query is to be created later right. 

### Docker
![[Pasted image 20250716105846.png]]
Let's say we have bought VM from digital Ocean.
what if comments get heavy and We want to create new service or load balance it ? Event bus need exact Ip and Port as creating new service will be automatically allocated by Digital Ocean. and Remember Our event bus have exactly only the place of post or query or moderation. What if new service is added ? We need to manually get to those things. and What if we bought new VM as our webstie was incredibly active in some duration of the time ? Now we need to do IP and port of the second VM. To save money what if it wasn't that much active in let's say 1 pm in night ? then we can sleep second VM. So, for that we use docker. 

Docker and Kubernetes solves the issue. 
It is tool for running a bunch of different containers and We give it some configuration to describe how we want our containers to run and interact with each other. Kubernetes handles communication between those containers. 

In Kubernetes we create something called cluster. The cluster is set of VM. Each VM is called node. and they are managed by Master. We tell Kubernetes to run the program for us. It takes our program and more or less randomly assign our program to be executed by one of the nodes. 
![[Pasted image 20250716111115.png]]
We can say to config file that pls run 2 copies of posts and also allow the copies of Posts to be accessible from network. We need to feed the configuration file to master. 

Kubernetes will automatically figure out communication. 
![[Pasted image 20250716112238.png]]
We just need to send request to common communication channel. Kubernetes also makes creating services, launching new copies and scaling easy and straightforward. 

We actually put event bus in docker also in our project also so.
