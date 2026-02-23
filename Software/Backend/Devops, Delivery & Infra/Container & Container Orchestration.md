### Docker
Docker is a container technology. Every container has its unit of software, code and dependencies to run the code. No extra thing required.
![[Pasted image 20241108225132.png]]
#### Why do we need it ? 
Just think some features are only available after some version like this feature
```
await ConnectDB() // this is only available after node version 14
```
Let's say we wrote a code. But are we sure that in service provider that node will be more than version 14? Docker make it sure. Another need is that when we work in group, it makes thing easier. Another Reason is may be we are developing something in python. For one project we may be using python 2 while for other we may be using python 3. How to manage it ? Docker helps it to manage.
![[Pasted image 20241108230506.png]]
#### Why not Virtual Machines and why Docker ?
Virtual Machines are like different machines running in our computer. Like Linux running in Windows. So why not VM and why docker ? Cuz VM uses way too much space and way too much duplication. Don't try to be smart. Use Docker.
![[Pasted image 20241108231400.png]]
While docker is such ![[Pasted image 20241108231420.png]]
#### How is Docker installed ?
Windows natively don't support Docker. So, While installing Linux is installed in Virtual Machine and Docker run in that. The goal is to use Docker Engine. Docker Desktop(an app) is just to use Docker Engine.
![[Pasted image 20241108233321.png]]
#### Image and Container.
Container are running unit of software while Images are the blueprint to make container. It is the images that contains the code and required tools/runtimes to run the code and it's the container that runs and execute the code using those tools. The gist is that we can create the image with all the code and instructions once and then we can use that image to create a container. For e.g. If we talk about node.js web server, we can define the image once and run it in multiple times in multiple machines and different servers. Image is the shareable packages. and container will be concrete running instance of such image.

#### images
It can be shared by colleague or from `hub.docker.com`. Try to use from that website as it is created and maintained by official `node.js` team. When we hit `docker pull node`, it will try to search for that thing locally but it will not be found. So it will search from library. From there it will create the container based on the image available in node.js `docker.hub`.
However that interactive shell of node isn't available to us at all. Why ? Because we can't directly use that we need to hit 
`docker run -it node` to run it and remember it is different node than that is available in our device. In fact, we don't need node at all. The thing is that if we have multiple container, then the code isn't being copied. Container just add the extra layer like running node and that is it.

Dockerfile
```js
// All this instruction are for setting up the image. Image can best set from start or from custom image from docker library. This one is from docker library.

FROM node:14 // from node means find that image locally or from library. So it says We want to pull node image. Here node is name and 14 is a tag, tag is optional though.s

//  which folder should we execute our command into container ? if it is set to /app then all the file will be copied to releated to this folder and all the commands will work from there.
WORKDIR /app 

COPY package.json . // so that we don't need docker to run npm i everytie we change anything. Only run npm i if package.json is changed.
  
// after doing install
RUN npm install

// which file should be copied from here to docker container ? The first dot is from where should we copy ? . means from the folder which has Dockerfile and second dot means where should we copy ? in container file explore which isn't visisble easily. It is standard practice to do it in /app
COPY . /app // it is direct here we could also specify ./ and it will specify from WORKDIR
COPY . . // sincee we have WORKDIR we don't need to write /app as we have that value in WORKDIR

// Why this is wrong ? Cuz this is the command to run the image. We want to run the container,not the image. Yes, we want to have code and dependecies in image but we want to run the container.Also if we start multiple container then we start multiple server so this is wrong
// RUN node sever.js

// the container doesn't expose the port to our local machine. So we won't be able to listen just because something is listening inside the container. So as soon as container is running, we are letting docker know that we want to expose port to our local system
EXPOSE 3000

  

CMD ["node", "server.js"]
```
Now we can run `docker build .`
```
docker build -g name:tag .

docker images // see all the images

docker rmi imageId imageId // to delete the image and images can't be deleted if there contains even a single container that is running or stopped.
```
#### run, start and delete container
```
docker run -p 3000:3000 -d -rm --name nameOfContainer imageId
-p means publish first 3000 means local port and second 3000 means docker port, rm means it will delete as soon as 

docker start imageId

docker rm imageName imageName imageName

docker images // see all the images
```
#### Attached and Detached Mode
We can use docker in attached or detached mode. Attached mode means we can we can see the process in foreground, i.e. we can see `console.log` in the terminal. We can see the terminal in foreground. But in detached mode, the process isn't running in the foreground, but background. That means we can't see the terminal and we can use terminal for other use. for `docker run` attached mode is default while for `docker start`, detached mode is default.
```
docker run -p 8000:80 -d imageId // d means run the container in detached mode.
docker start -a imageId // this is to use for Attached Mode
```
 So what if detached mode is done and we want to attach ?
```
 docker attach imageId or imageName
```
We can also see only logs with `logs`.
```
docker logs imageName
```

But if you see what if we need interactiveness ? then we can use `docker run --help`. We may need it for many python projects. 
```
docker run -i -t imageId // i for interactive and -t for its own terminal
```
#### Copy the code to or from container
In some case we may want to copy the files by `cp` command into container as we wouldn't want to restart entire container. But it isn't desired as it may break any code .It could be done for some configuration files. Instead there are another way to manipulate the container rather than copying.
#### Docker Hub
We share images and not container. Sharing a Build Image is preferred.
![[Pasted image 20241110191246.png]]
#### External Data Storage/Volumes
There are three kinds of data. First is source code, second in temporary data like formData or data generated while using website, third is permanent data like user data and should be stored in database. Volumes are folder in hard drive which are made available in container.

Container are read-write while images are read only.
![[Pasted image 20241114155952.png]]
That extra layer of container kinds of mirror the file system of image without copying the files. While in image it is only read only. For Container Docker has read and write access also and is able to manipulate that file system without changing it in the image just by changing in that extra layer of Container. When we change the file, container track change that was done and combines with the files of image to give the final files.

![[Pasted image 20241112154112.png]]
###### Anonymous Volumes
Anonymous Volumes are completely managed by Docker. We don't know where the files are kept in hard-drive. It only exist as long as container exists.
###### Named Volumes 
```
docker run -p 8000:80 --name nameofcontainer -v feedback:app/feedback nameofimage
```
here the `feedback:app/feedback` is the name of the volumes. the second part is about which folder or files we want to persist. and first part is what we want to give name.

###### Bind Mounts
Bind Mounts is just like volumes with some differences. However they aren't volume. Just like word mount suggest, we mount our source code to docker when needed Volumes are managed by Docker and we don't know where the stored file is in our local computer. Bind Mounts are managed by us and we, the developer set the path how the container internal should be mapped to our local computer. Container can't just write from volume but read from there. So, we can put the source code in bind mount and let the docker know that read the code from bind mount and not from image snapshots `COPY . .`.
```
docker run -p 8000:80 --name nameofcontainer -v feedback:app/feedback -v "absolute path of the folder that we are writing the program:/app"  nameofimage
```
Just look at the second volumes. second part is where should that first part go. first part is absolute path of the folder we are working

When we do this it renders this steps useless.
```
COPY package.json .
RUN npm install
COPY . . 
```
why ? cuz we are telling the docker to use the code from our local machine instead of the code that exists in this docker. and in our local machine there is no `node/modules`. How to counter that ?
```
docker run -p 8000:80 --name nameofcontainer -v feedback:app/feedback -v "absolute path of the folder that we are writing the program:/app" -v /app/node_modules  nameofimage
```
Docker always evaluates all the volumes that we set. Here, we have `/app/node_modules` have longer path than `program:/app`. So `node_modules` from former one is used.
###### Read only Volumes
By default volumes are read and write access.  We can do that by writing `:ro`.

> [!NOTE] Why do we need `COPY . .` if we use bind mount ?
> Cuz it is needed in production.

###### dockerignore
```

```
###### Arguments and Environment Variables
Docker support build time argument and run time environment variables.
In Images
```
ENV PORT 80
EXPOSE $PORT
```
We can also do it while building container
```
docker run -p 3000:8000 -e PORT=8000 -e 
```
If we want to use `.env` file
```
docker run -p 8000:3000 --env-file ./.env
```
For Arguments
Arguments are dynamic, means we can change the value of DEFAULT_PORT while building container. First one is default one
```
ARG DEFAULT_PORT=80

ENV PORT $DEFAULT_PORT
EXPO PORT
```
In Command line, we can build another container by replacing that port number while building so we don't have to touch Docker file.
```
docker run --build-arg DEFAULT_PORT=8000
```
#### Network
How to connect two containers ? What kind of two connection are there in docker. 
###### Container to Container
It is best practice to have one container for one job. Like connection to mongo should be one container.
To start a mongoDB container first run this 
```
docker run --name mongodb mongo
```
then we can inspect using `docker container inspect mongodb` and we will get the ip address of the mongo db container from network Settings. We need to replace the localhost with that ip address. and make the container again.
```
docker run --name nodecode
```
But this shit not convenient. There is easier way.

Container Networks
First make the networks through which we can connect all the container for use
```
docker network create networkone
```
Then, we need to create a mongo build
```
docker run --name mongodb --network networkone 
```
then build the image again for our source code. and change the code for `mongoose.connect` to `mongo` db image.
```
docker run --network networkone
```
###### Container to www
It just works. We can send http request from docker
###### Container to Local Host machine
e.g. It doesn't work with mongoose. So instead of writing `localhost` we should write `host.docker.internal`. It should be written for every place where there is `localhost`.



#### Multi Container Application
Like said previously, we need to have one docker or image for one service like one for mongo, one for node and one for react. So, make a network with certain name and make the mongo container with that network, also nodejs and Reactjs. and in the same container instead of `localhost` or `host.docker.internal` write the name of the container that you want to connect. Like we want to connect to mongodb container then the connection string is `mongodb://mongodb:27017/demo`. For react, only development server is running in docker, not code. So, we still need to publish that as we need to connect to code running in browser.
For mongodb data persistence, we need to use volumes like `data:data/lib` while running the container. And we can also add the user and password. just look at website and write the connection string in this format
```
	mongodb://user:password:mongodb:27017/demo?authSource=admin
```
For nodejs we need to persist logs file and live source code. 
for that we need to write this. we need to keep using same node_modules from container even when we start the container and we need logs persitence. and use bind mount to use latest code.
```
docker run -v absoulte path to backend folder:/app -v logs:/app/logs -v :/app/node_modules
```
also use `.dockerignore` also
```
node_modules
Dockerfile
.git
```

for react we can use bind mount for react to get latest code. 
```
docker run -v 'absolute path':/app/src
```
#### Docker Compose
It works best for multiple container in single host. Service just means container, however names are going to be different. The file `docker-compose.yaml` exist at root meaning sibling to backend and frontend folder.
```
version: ""
services:  // it denotes the services like that

	mongo:
		image: "mongo" // we have docker run --name mongodb -e MONGO_INIT_ROOT_USER=root -e                                      MONGO_INIT_ROOT_PASSWORD=root123 -v data:/data/db --rm -d --network network-                           name mongo // we don't need to specify --rm as it is already inscirbed in                              yaml means if we stop the yaml, it goes already, and it is already in                                  detached mode
		volumes: 
			- data:/data/db
		environment:
			MONGO_INIT_ROOT_USER: root
			MONGO_INIT_ROOT_PASSWORD: root123
			- MONGO_INIT_ROOT_USER=root  // alternate sytax
		env_file: 
			- ./env/.env // direction from yaml
		network:
			- networkname // however it is optional, as ddocker componse already make the network when                              creating the yaml file. They all are combined in same network
		
	backend:
		build: ./backend // where is Dockerfile ?
		build: // another syntax
			context: ./backend // it is also the path where image in generated after that means we                                       need to ensure that what ever dockerfile point at, context should                                      have it 
			dockerfile: Dockerfile // optional is Dockerfile is named as such. sometimes it is named                                         as Dockerfile-dev also so
		port: 
		 - "80:80"
	    volumes:
		    - logs:/app/logs
		    - .backend:/app // we don't need absolute path at all.
		    - /app/node_modules
		env-files: 
			- ./backend/.env
		depends_on:
			- mongodb


	frontend:
		build: ./frontend
		port:
			- "3000:3000"
		volumes:
			- ./frontend/src:/app/src // it is bind mount
		depends_on:
			- backend // it is optional though
		std_in: true,
		tty: true,
			



volumes:
	data: // we should make this for named volumes
	logs:
```
To start the docker compose we need to type this
```
	docker-compose up -d
	docker-compose down // stop the services and remove all container as it removing auto happens
```

> [!NOTE] Thing to Remember
> Once docker compose is run, at first it build all the images. But once we stop and rerun docker compose, if DC finds that image is already build, it uses that. So, sometimes we need to rebuild the entire docker compose. We can achieve it using `--build`. We can also just build the image and not start the container with `build`. It is different from previous one. Infact, `up` already includes `build`.
#### Utility Container
Not an official term at all. just a idea by instructor.
We can use `docker exec` to run the command inside the container if it is running. At first we can run the node docker container and use exec command in running container to run extra command in that container.
Like we first initialized the node container
```
docker run -it -d node
```
Here node container in running and waiting for interaction. However it is running in detached mode. Despite that, it does accept command using `exec` command. `docker exec -it nameofnodecontainer npm init`. `-it` is there as we do need interaction for the `npm init`. Then node project was initialized inside the container. Infact, we can also run `exec` command to replace default command also. We can replace the node initialization command using `exec` command.
Like we can use Docker to run only npm init here `docker run -it node npm init`. Here the default is node initilazation in interactive mode which enters into repel but we replace that with `npm init`.
Now we want to install the node program in container and would want to mirror it in local computer. By doing that we avoid the hassle of installing the files in our computer. At first we should create the `Dockerfile`.
`Dockerfile`
```
From node:some version
Workdir /app
and we aren't going to write anything after as we would like to give all the control to us We could have wrote CMD ["npm", "init"] but we can use docker run -it nameofimage npm init. What will happen. remember npm init will replace run nameofimage and we can initiliaze the npm project.
```
Here is how to mirror the files of container to local computer. 
```
docker run -it -v "absolute path":/app nameofimage npm init
```
We can see that `package.json` was installed.
After name of image, if we give the command like `npm init`, it replaces the original command of CMD in `Dockerfile`. And we can make our container extra secure by using executables. Executables just append that thing just besides the name of image in command like. Such as if we have `EXECUTABLES ["npm"]`. Now, we don't need to type the npm now after name of image. By doing this we provided security to the container. Who know we might have deleted something ? as it was mirroring to local computer. so now by typing this `docker run -it -v "absolute path":/app nameofimage init`, npm is appended after name of image. so it becomes `npm init`.

And guess what. We can use Docker Compose to achieve this. Remember it is just the command that open interaction terminal, use bind mount and build.
Dockerfile
```
FROM node:
Workdir /app
EXECUTUABLE ["npm"]
```
```
version:
services:
	npm:
		build: ./
		stdin_open: true
		tty: true
		volumes:
			- ./:/app
```
But we can't use use `dokcer-compose up` as it is just meant to really just bring up services defined in `yaml` file. However we also get `dockercompose exec` command to run the command in already running container which were created by `docker componse`.  And that typically means application containers. Then we should run `docker-compose run --rm npm init `. With `run` the container aren't removed automatically. Here npm in about the services and not the random thing. the init is appended to the Docker file executable and not the service. remember this. Here name collision might create a tension. In short it is not services npm that is appended but the executable.
#### Deploying Containers
We shouldn't use bind mounts in production. and push the container to ECR and deploy using ECS with tasks and service and load balancer.
![[Pasted image 20241224132347.png]]

### Summary
![[Pasted image 20241218181052.png]]

### Main features

### Kubernetes