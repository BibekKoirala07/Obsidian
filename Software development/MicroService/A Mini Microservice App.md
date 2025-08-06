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
We don't' create pods with the style above mentioned. Rather than creating the pods directly, We are going to use Deployments. It is the Kubernetes Object which is intended to manage the set of pods(1 or 10). Each of these pods will be identical in nature. The deployment has two jobs. Lets say one pod automatically disappear. The Deployment will recreate one.
![[Pasted image 20250718135938.png]]
In anther case is updation of code.  Let's say we have version one of code and We need to update it to version two. Then deployment takes care of that. Overall, Deployments acts like a manager of Pods. 
###### Create a Deployment
Inside k8 directory, create a `posts-depl.yaml`. `depl` means deployment. Inside that, we write config, we want to run some number of pods of posts image. 
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
	name: posts-depl
spec: // how the deployment should behave
	replicas: 1 // no of pods
	selector:
		matchLabels:
			app: posts
	template:
		metadata:
			labels:
				app: posts
		spec: 
			containers:
				- name: posts
				- image: stephengrinder/posts:0.0.1
```
Now apply it to clusters. How ? `kubectl apply -f that.yaml`
###### Common Commands Around Deployments
 When we delete the pods created by deployments, then deployment will automatically make it appear again. ![[Pasted image 20250718152503.png]]
 So if anything starts to go wrong, Kubernetes will handle that thing. In simple words. start deployments -> pods are started -> they can't be deleted(which is nice thing) -> delete deployment -> pods are gone
==this way is optimum way of doing things==
###### Updating Deployments. 
Remember we can update the code/version ?  ![[Pasted image 20250718160114.png]]
There are two different ways. 
First way is this
![[Pasted image 20250718160443.png]]
We don't do this often. Here we change the source code, and rebuild the image to new container and in `posts.dep.yaml`. we rebuild the deployment also. This is how it is done.  
==not used that often==. the reason being `image: stephengirder/posts:0.0.5`. You see that 0.0.5. We would like to avoid that. It may cause error right ? So we go to second one. Simple way is to tell kubernetes to updates itself everytime we create a image right ? 

Second is better
![[Pasted image 20250718161325.png]]
You see that version. We would like to avoid that. 
###### Preferred method for Updating Deployments
![[Pasted image 20250718161424.png]]
We would like to remove that version then docker assume that we are using the `:latest` version. 

###### Networking with services
How to we make the request to that application which is hidden behind docker and again kubernetes ? We creates services using config files just like pods and deployments right ? Service is another kinds of object like pod or deployments. We are going to set up services to setup communication between the pods or to get access to our pods outside our services. 
![[Pasted image 20250718162641.png]]
Every pod or deployments will have matching services associated with it. 
Behind the scenes there are different kinds of services
![[Pasted image 20250718163011.png]]
We only use Cluster IP and Load Balancer Service in daily basis. 

Tricks Question ? 
What if we want communication between pods ? We use Cluster Ip
What if we want to communicate from browser(From browser) ? We may use Node Port or Load Balancer. 
![[Pasted image 20250718163409.png]]
== We will use Cluster and Load Balancer ==
###### Creating A Node Port Service
Remember we said we aren't using Node Port That much right ? 
We are just making things for demo. 
We can create `posts-srv.yaml.
![[Pasted image 20250718163700.png]]
`srv` means service
```yml
apiVersion: v1
kind: Service
metaData: 
	name: posts-srv
spec:
	type: NodePort
	selector:
		app: posts
	ports: 
		- name: posts
		- protoco: TCP
		- port: 4000 (remember posts run at 4000)
		- targetPort: 4000
```

![[Pasted image 20250718164132.png]]
the port and targetPort doesn't have to be identical. 
###### Accessing NodePort Services
When we do `kubectl get services`, we might get something that starts with `3****`. thirty thousand something. It is nodePort.  It is randomly assigned port which is used to access the pod from outside the Cluster. 

We can access it by
```
	localhost:30402/posts
```
this entire node port thing is just for deployments. 
###### Setting Up Cluster IP Services
The goal of Cluster IP is to expose the pod to other pod in the cluster. In out things, we have many services like posts, comments, event bus and other. The goal ? It is simply to communicate. 

The thing is we could have one node with two pod and these two needs to communicate. But they can't communicate for now. Well they technically can by doing something like `http://Ip/events`.
![[Pasted image 20250723111525.png]]
We how are we going to know the IP ? We never know what IP address the Pod is going to be assigned. It will n't be assigned ahead of time. It isn't going to be `localhost`. So, How are other services going to communicate to event bus. So Cluster IP Service will govern the access to Event Bus Pod. So We do this 
![[Pasted image 20250723112117.png]]
Just communicate through IP Service. So, the goal is 
![[Pasted image 20250723112259.png]]
###### Adding Cluster IP Services
We can create Cluster IP Service directly is yaml file. It is choice of people but we will do it this way.
```yaml
// We have that making pod yaml here. 
---
ApiVersion: v1
kind: Service
metadata:
	name: event-bus-srv
spec: 
	selector:
		app: event-bus //it is in above in yaml
	type: ClusterIp
	ports:
		- name: event-bus
		- protocol: TCP
		- port: 4005
		- targetPort: 4005
```
Now we can do `kubectl get services`.
Remember You need to take care of Name collision.
###### How to communicate between services
Now after so much hustle, Remember we still have 
```ts
await axios.post("http://localhost:4005")
```
So what is new url  ?
![[Pasted image 20250723115627.png]]
The exact name is going to be name of Cluster IP service. We can get name by `kebectl get service`. In this context, it is `event-bus-srv`. So the URL is going to be 
```ts
await axios.post("http://event-bus-srv/4005")
```
Here we are communicating from event bus to posts.![[Pasted image 20250723115944.png]]So just like that we can update the service addresses.
###### Verifying Communication
 We can verify it using different medium.
###### Adding Query, Moderation and Comments. 
###### Testing Communication
###### Load Balancer Services
It is about React App. ![[Pasted image 20250723160920.png]]
The goal is to make the react app working in pod. There are two options. 
We aren't going to do this 
![[Pasted image 20250723161003.png]]

But we are doing this one![[Pasted image 20250723161134.png]]
The goal of that load balancer is to provide one single entry points to our entire cluster. The React application is going to load balancer service and route to appropriate pod or Cluster IP Service. 

###### Load Balancer and Ingres
![[Pasted image 20250723161552.png]]
Technically Ingres or Ingres Controller are two different things. 

The goal of load balancer is to get traffic into single pod. 

On the Other Ingres(they are different but for the sake or learning, here they are assumed to be same).

The goal of LoadBalancer Service is to tell the Cloud Provider to put Load Balancer outside the cluster with the goal of providing route to pod. 

![[Pasted image 20250723162639.png]]
Load Balancer just get request to cluster and later ingress controller handles it

###### Installing Ingress-Nginx
The project is `ingress-nginx`. 
![[Pasted image 20250723162948.png]]
We are using `kubernetes/ingress-nginx` and not other.

##### Installing Ingress-Nginx
![[Pasted image 20250723163703.png]]
make the ingress-srv.yaml and write the yaml there

![[Pasted image 20250723164111.png]]

###### Hosts File Tweak
When we use kubernetes to host one single application at one single domain. But we can also host multiple application with multiple domain in one single cluster. But with kubernetes we can host ton of infrastructure. We can have some app tied to `my-app.com`, some other app tied to `post-tracker.com`. The infra for all of those domain can be hosted in one single kubernetes cluster
![[Pasted image 20250723165129.png]]

Here for development purpose, we should be able to tweak in such a way that we can route `posts.com` to our device. Go to `etc/host` and add the line
```
127.0.0.1 posts.com
```
and we don't need to go directly to pod. and just type 
```
posts.com/posts 
```
Instead we wrote number URL and take the URL and ingress-nginx is going to take the request to appropriate service and it is going to take it to appropriate pod anyway. 

###### Deploying the React App
So we make container and push to docker hub and create yaml to create deployment and make service and pod. That is it. 

Ingress Controller has no idea that the method(method like GET, POST) exists. Just path exists for for Ingress Controller.  


==We don't have to re-build the docker image every time we change the code==. 

###### Unique Route Paths

###### Final Route Config

###### Introducing Skaffold. 
This are the steps that we will go through anytime we change the code of our deployment. 
![[Pasted image 20250724135554.png]]
You see, it is a hassle, right ? 
We will be using skaffold. It is a command line tools which we will use to automate kubernetes.
![[Pasted image 20250806112945.png]]
When skaffold is installed, we need to write another config file also. That config file is used to tell the skaffold how to manage sub project inside our cluster
![[Pasted image 20250806113821.png]]
The config we write inside `skaffold.yaml` is rather similar to config written to setup `kubernetes` cluster. Everything we write in `skaffold.yaml` isn't  applied to kubernetes. Instead it is consumed by skaffold. skaffold is the tool that runs outside of our cluster. A lot of skaffold we are going to write are going to be identical. So just learn it once and use it everywhere. 
```yaml
apiVersion: skaffold/v2alpha3
kind: Config
deploy:
	kubectl:
		manifests:
			- ./infra/k8s/*
build:
	local:
		push: false
	artifacts: 
		- image: bibekdockerId/client
		- context: client
		- docker:
			- dockerfile: Dockerfile
		sync: 
			manual:
				- src: "src/**/*.js"
				- dest: .
```
That `manifests` tells skaffold to let us watch the kubernetes config file inside infra folder named `infra/k8s`. Any times we make change to those files, skaffold is automatically reapply the changes to our kubernetes cluster. skaffold will create all the object and delete when it is run and stopped.  

That local one means we aren't going to push our project to docker hub automatically at all and artifacts means that there are something inside our project that it needs to maintain. Like there is some pod that is running code out of client directory(context) inside our project. 

So whenever we make change to js file in that client folder, skaffold will try to update our pod. It does by two ways. Let's say if we change the `js` file as mentioned in `manual` the skaffold is going to take that file and directly going to throw it in pod(copy). And whenever we change the code that doesn't belong to the path described by `manual`, skaffold is going to rebuild the image. 

Now, do it for other docker file like comments, posts, event-bus
###### First time skaffold startup
Now we have fully scrabled skaffold app
```
skaffold dev
```
###### Few Notes on Skaffold













Kubernetes will automatically figure out communication. 
![[Pasted image 20250716112238.png]]
We just need to send request to common communication channel. Kubernetes also makes creating services, launching new copies and scaling easy and straightforward. 

We actually put event bus in docker also in our project also so.

###### Kubernetes Tour
Kubernetes is about stability. 
