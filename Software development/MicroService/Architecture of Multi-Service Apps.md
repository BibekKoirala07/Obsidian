### Big Tickets Items
![[Pasted image 20250806121928.png]]
We also had lot of Pain in the ass. 
![[Pasted image 20250806122219.png]]
and we are going to solve that painful thing by these things.
![[Pasted image 20250806145526.png]] 
### App Overview
We are going to build this ![[Pasted image 20250806135830.png]]
The app is similar to stubhub ticketing platform.


### Resource Type
![[Pasted image 20250916112025.png]]
### Service Type
![[Pasted image 20250916112639.png]]
![[Pasted image 20250916112725.png]]
### Events and Architecture Design
![[Pasted image 20250916112954.png]]
![[Pasted image 20250806142123.png]]
that common is shared common library. that common in npm module. 

### Auth Service Setup
![[Pasted image 20250916113432.png]]
### Auth k8s Setup
any time we create a deployment, we usually want to create the service to go along with it. Kubernetes service to give access to pod.  We could create a sperate k8s but normally write it in same file pls.

### Adding Skaffold
By doing what I did in dev, I made scaffold. Now change something and see if it restart. 

### Ingress-Nginx Setup
 To access the anything inside cluster, We have two option. Either setup node port service or ingress service(just a routing rule tied to nginx). Ingress handles routing inside our clusters.
For that we need to go to 
```
	kubernetes.github.io/ingress/nginxf
```

### Hosts File and Security Warning
![[Pasted image 20250916141119.png]]