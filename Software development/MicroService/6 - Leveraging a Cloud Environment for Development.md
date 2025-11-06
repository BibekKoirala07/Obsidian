### Remote Dev with Skaffold
We will use Google cloud as it will solve our problems. 

In our local computer, we normally run kubernetes  

We have single kubernetes node and on there we could possibly have many different pods. At present we only have one pod, that authentication pod, but we possibly could have many pods later. We could access that using ingress-nginx server in browser. 
![[Pasted image 20251029094443.png]]

But in development when big VM are used, we sourced them to Google Cloud or AWS. We will be still be running browser in our machine but our entire kubernetes will be running in Google Cloud VM. So we will have node somewhere out in the cloud that is running all of out different Pods. So here we need to understand what exactly is scaffold is doing for us. It helps us to setup everything in google cloud easily. Scaffold was developed by google. So we would want to tell Scaffold by telling it that we want to host some pods in google cloud as opposed to local node in our machine. Remember skaffold creates deployment and services but also handles the changes in our project. 
![[Pasted image 20251029100814.png]]
But when we change the non-synced file, it is little bit complex. So normally, the code is synced. The configuration is what is unsynced. like `package.json` in our case. If `package.json` is changed, then it is assumed that we would want to rebuild the image. 

![[Pasted image 20251029101211.png]]
###### Kubectl Contexts
![[Pasted image 20251029144813.png]]


![[Pasted image 20251029152427.png]]