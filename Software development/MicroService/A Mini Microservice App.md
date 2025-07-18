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
### Kubernetes
######  Kubernetes Tour
It is tool for running a bunch of different containers and We give it some configuration to describe how we want our containers to run and interact with each other. Kubernetes handles communication between those containers. Whenever we want to use kubernetes, we need to have set of docker images which are ready to go. 

In Kubernetes we create something called cluster. The cluster is set of VM. Each VM is called node. and they are managed by Master. We tell Kubernetes to run the program for us. It takes our program and more or less randomly assign our program to be executed by one of the nodes. A Node is Virtual Machine. So essentially a computer that is going to run number of container for us. If we are running Kubernetes in our computer, then it is extremely likely that we are just using one node(as it is one VM). We can use two VM only if we are putting those things in cloud. However, the theory is same for one or thousand Node. 
![[Pasted image 20250716111115.png]]

![[Pasted image 20250718113721.png]]
To create the container out of Post image, we are going to create a configuration file. We can write explicit direction in that file. Pls make the container from our image and host it in some node. We can also say pls allow a copies of posts to be accessible from network(Network issue). We feed that config file to Master(Kubernetes). The clusters are going to read the config file and do its tasks. 
Kubernetes tries to find the image(from local. if not available , then to docker hub). It creates the two container and randomly distribute(there is science but for now randomly). Each container that is created and is hosted is going to be inside something called pod. The pod and container are same. However not technically same. The pod can wrap up container. and also can have two container in single pod. 

![[Pasted image 20250718114402.png]]
In order to manage these two pod, Kubernetes it is going to create something called Deployment. This deployment is going to read in config file. That deployment is going to be in charge of all the pod. If something happens to pod(like crashing or something), that deployment is going to recreate the pod and make sure pods are running successfully.
![[Pasted image 20250718114923.png]]
 Now for that network issue.  (we had to allow to access the services from other pod). It creates services. Services is not necessary a running server or whatever. Instead it is something the gives access to running pod inside the clusters. It is about handling networking issue in microservices. It is like Send me a request and forward it to relevant Node(pod(container)). What port is container running, What IP ? Well Service will handle it. 
 
![[Pasted image 20250718115656.png]]
Let's say in future, we create another pod (let's say event bus), rather than reaching out to container directly, it is going to reach out to service(which will be available at easy to know url or node. ) It won't be like `localhost:3000` or whatever. It will be more like `PostService`. 

###### Terminology in Kubernetes
![[Pasted image 20250718115805.png]]
###### Notes on Config files
Remember that config files which is used to create node, services and deployments. It is written YAML syntax. 
![[Pasted image 20250718120440.png]]
We are store config files with project. we are going to commit these to git. 

###### Creating a Pod
We need to rebuilt the posts service(that we made in project). We always need to rebuild the docker container. We get something like 
![[Pasted image 20250718120952.png]]
See that last line ? Well it isn't some magic, we actually typed that . 
![[Pasted image 20250718121301.png]]
Now let's write config to create pod using that image/container. 
create a infra directory. create `k8s` directly inside infra. Create `posts.yaml`. 
![[Pasted image 20250718121508.png]]
```yaml
apiVersion: v1
kind: Pod
metadata:
	name: posts
spec:
	containers:
		- name: posts
		- image: stephegrinder/posts:0.0.1
```
We want to create pod with name `Pod` and have exactly one container with name `Posts` and build that container with image of `Ste...`
Now save it and run the `yaml` file to create a new Object.
We can inspect different pod and cluster. so yes do that. like `kubectl get pods`.  
###### Understanding a Pod spec. 
![[Pasted image 20250718122356.png]]
but for out project, Pod is like a container. usually it isn't. We need to remember that. In that last image section, use version otherwise it is always `latest`. remember docker ? 

###### Common Kubectl Commands
Kubernetes is about running bunch of containers together. 
![[Pasted image 20250718134525.png]]
###### Time saving alias
you can use `k get pods` instead of `kubectl get pods`. You can actually do it by putting alias in our computer.  

###### Deployments
Deployment is Kubernetes Object. 
We don't' create pods with the style above mentioned. Rather than creating the pods directly, We are going to use Deployments. It is the Kubernetes Object which is intended to manage the set of pods(1 or 10). Each of these pods will be identical in nature. 
![[Pasted image 20250718135938.png]]

Kubernetes will automatically figure out communication. 
![[Pasted image 20250716112238.png]]
We just need to send request to common communication channel. Kubernetes also makes creating services, launching new copies and scaling easy and straightforward. 

We actually put event bus in docker also in our project also so.

###### Kubernetes Tour
Kubernetes is about stability. 
