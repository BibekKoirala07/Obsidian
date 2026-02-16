![[Pasted image 20241107165714.png]]
The common flow are 
![[Pasted image 20241107165817.png]]
![[Pasted image 20241107170520.png]]
There are many CI Providers in today's world. The Providers is the company who will provision CI servers for you. CI servers is the virtual machine that runs in the cloud that automatically clone your project and run the test and make sure the code that you are adding isn't breaking anything.
![[Pasted image 20241107170616.png]]We are using Travis CI.
![[Pasted image 20241107171446.png]]

```js
// this is for .travis.yml

language: node_js // need to specify this as this is only options
node_jd: 
	- "8" // specify the version in array.
dist: trusty // It is a base image for virtual machine that will be used. Just like we have our one like windows, macos, we need to specify for our VM also, trusty is a small version of linux. We just have very small test to do which is test suite
services: 
	- mongodb
	- redis-server // when ever travis sees this services and the distribution, it boots up Virtual Machine with own instace of mongo and redis server

// from now on it will be more project specific, upto now it was common for all node.js, may be serivces was different
env: 
	- NODE_ENV=CI
	- PORT=3000
cache: 
	- directories 
		- node_modules // telling travis that cache it as we don't push it to github however it may look like why do it ? everytime code is pushed travis uses npm install, if the node_modules are same. it doesn't run that thing. if not it replaces the existing modules.
install: // list of commands that we want to execute
 - npm install

 scripts: // just like install we can list no of command we can execute but for testing here we start up our server and execute some test.
	 - nohup npm run start & // & means we are able to run the command even after running npm run start. & doesn't work in windows. only in MacOs and Linux and nohup means no hanging up. Travis Ci is notorious for just stopping the process. so nohup is added means even if the terminal is hanging up, don't kill the process.
	 - sleep 3 // sit around for 3 second cuz if not put test instance might access services before intitialization
	 - npm run test // then only run test
```
![[Pasted image 20241107181831.png]]
#### Setting in Travis CI
We can use different .env variables for CI.
```
NODE_ENV=CI
MONGO_DB=mongodb://127.0... something/blog // use docs for more but it id defualt like in development
REDIS_URL= // it is default like in production . go to docs for more
```
![[Pasted image 20241107185114.png]]