10 years ago we used Nginx for all these purposes. No special builds, no modules, just simple lonely Nginx setup to do all that work: forwarding, proxying, caching, balancing, filtering, authentication, sessioning, even handling the versioning.

![[Pasted image 20260209211532.png]]
They all forward the request but they exists for different reason and solve different problems. 
### Reverse proxy
Before knowing reverse proxy, we need to know forward proxy. 
![[Pasted image 20260209211834.png]]
A corporate web filter filters which sites that employees can visit. Or a privacy services that mask the IP address. A reverse proxy is opposite. 

When we hit the request to major services like Netflix and things like those. We aren't hitting the request directly to Netflix. We are hitting the request to reverse proxy.
![[Pasted image 20260209212113.png]]
Only reverse proxy is visible to client. 
![[Pasted image 20260209212145.png]]
Why bother with this extra layer ? 
![[Pasted image 20260209212245.png]]
![[Pasted image 20260209212346.png]]
![[Pasted image 20260209212420.png]]
this is useful but also limited. which brings us to load balancer. 
### Load Balancer
Once the application grows and you add a new server. You add another server. But there is a problem. The problem is how do requests get distributed ? ![[Pasted image 20260209212646.png]]

![[Pasted image 20260209212718.png]]
![[Pasted image 20260209212728.png]]

![[Pasted image 20260209212824.png]]

![[Pasted image 20260209212853.png]]

![[Pasted image 20260209212906.png]]

![[Pasted image 20260209212922.png]]

![[Pasted image 20260209212937.png]]

### API Gateway
The difference between API Gateway and above two is about purpose. But they all sit between client and server. 

But what problem does it solves ? 

![[Pasted image 20260209213614.png]]
![[Pasted image 20260209213634.png]]


So why is everything so blurred in real life ? 
![[Pasted image 20260209213749.png]]
You see Nginx is All three with some extension(open-rusty) So it is blurry. 
![[Pasted image 20260209213916.png]]
![[Pasted image 20260209213925.png]]

![[Pasted image 20260209214017.png]]


Real world architecture. It is just examples. It
![[Pasted image 20260209214123.png]]


![[Pasted image 20260209214240.png]] Nginx at each services