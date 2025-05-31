```
redirect("/")
```
is kind of error. remember to not put this in try catch. as it will be considered as error and not as redirect. Next treats redirect as error(special kind of error).

#### Full Route Caching
why we build the next app `npm run build`. next decides whether the route is static or dynamic, if the page is static, then page is rendered and result is stored. It happens in build time means at time when we put page in vercel or any other things.so, so, data at that time is shown ad exact file is shown to every one. in production, users are given this result.

#### Information to know
![[Pasted image 20240420191909.png]]

#### 
1. At first when browser request for something, then, at first Next Server generates some HTML file. and both client and Server components is rendered in HTML in server. and resulting HTML file is sent to browser. It barely contains any javascript. it contains very few js.
![[Pasted image 20240420133817.png]]
2. Then HTML file is sent to browser like and it contains `script`  tag which asks for js to be sent to Next Server

3. Then in Next Server extracts all js from Client component and send it to browser and it is executed in browser. ![[Pasted image 20240420134714.png]]
