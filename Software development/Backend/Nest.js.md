![[Pasted image 20250113161735.png]]
Any request first comes at middleware and then guard and then interceptor and later pipe. They can be used in global scope or in controller scope and in request scope. Interceptor works when the response is being sent also. Interceptors works on request and response time while middleware just works on request time. Pipes works in data validations and data transformation.
### Theory
We can have two types of modules. They are modules without controllers and module with controller. Everything that can be injected or that is ought to be injected should be made providers. For something to be injected, it should be imported in module. If something is to be made as injectable, it should be exported. Like here in Album Module, Album Service uses Users Service. That means we need to import User Module in Album Module.
![[Pasted image 20250117121853.png]]

### Headers and Header
One is Request Header and one is Response Header.
### Providers Types 
![[Pasted image 20250113171237.png]]
![[Pasted image 20250113171331.png]]

### Dependency Injection
In imperative approach. Lets say we have User class and Account class. For User class to run, we need Account class. In Imperative approach, we would have to pass Account in User class. We would have to do this.
```
const account  = new Account();
const user = new User(account)
```

in Declarative Approach, we use Dependency Injection. DI works on IOC principle. It just means that we give control of managing dependency to framework.
![[Pasted image 20250117125929.png]]
In IOC container, we put class instance(dependencies instance) that can be used as dependencies. Any thing that can be used as dependencies is called providers. In above figure. Account, Wallet etc. are providers.
![[Pasted image 20250117130512.png]]
Let's say we want to use Account class in User. We first save it to IOC Container and provide the reference through injection token in class User. But the things is that you can't use that Account Class anywhere you cuz you used `@Injectable` in that class. There is scope for that. Where is it registered ? Who made it ? That is module. and it is registered by provider tag in module. We need to know about scope of module and providers.

If we want to put provider instance in IOC container so that it can be injected, then we should put that provider in some module so that it can be injected through module. Every module has specific injector. and providers can be injected only one time. The injector also perform the job of finding and giving the provider instance to other module when requested in run time. Any time nest js need to inject the dependencies anywhere. It takes it from IOC injector

If I want to put instance of providers in IOC container, first we have to register that from module. That provider should be part of the module. For every module, IOC container have injector for each module. The thing to remember is that in one module, if there are multiple providers. they can inject each other as scope is same.

We can inject class and normal value in any provider we want.
For async value injection we need to use `useFactory : async` Till the use Factory doesn't resolve, any controller which use that value in its constructor will not run at all.

### Injection Scope
There are three scope in Injection Scoping. The default one is `DEAFULT`. which say wherever that provider is used, the instance is same. you can use see it by using `console.log` in constructor in that provider. it just happens only one time. you can see it. But we also have option for `REQUEST`. for this the instance is create whenever new request comes in. and wherever that comes in new instance is created and is destroyed once response is sent. it happens for every request. 

> [!NOTE] Info
> Whatever is connected to that provider with scope option `REQUEST`. It also becomes the scope option with `REQUEST`. Let's say we have controller which has that provider injection. then that controller is also created everytime request comes to that controller. You can see it by doing console.log in constructor.

Another one is `TRANSIENT` scope. So what happens is wherever that provider is used, that number of instances is created. Like if there are album and user controller use that provider. then two provider are made for that controllers. you can use `console.log` to see that also.

### Services
### Modules
We have 4 types of modules in nest js. They are like folders. Following are the types of modules
###### Featured module
It involves one features like user features. Like creating entire routes for users and we may have specific controllers, services, routes, etc. We use this to organize our project.
###### Shared Modules
Let's say we have User Service in User Module , and we need to use it in blogs Module. What should we do ? we need to import User Module and User Module must export User Service. Then only we can use User Service in Blog Module.
###### Global Modules
For Global Module, we don't need to import. but we need to import it in App Module. and it will take care. Now we don't need to import rather we can use it directly. remember we can only use what is exported from that module. Like may be User Service. App Module is root module.


> [!NOTE] Note
> Let's say we have child module job-application module in Job module then we don't need to write job-application module in App Module. Nestjs graph dependency will solve it. and Let's say Job application module have provider as Job application Service and it exports it. Now Job Module import Job application module. Now what can be done  ? Well, Job Module can use Job Application Service right ? The thing is that Now it can export that Job Application Module and other will also be able to use Job Application Service. No need to import Job Application Module directly.
### Dynamic Module


### We can pass routing to Module also / Router Module
Let's say we have three controller in jobs module. They may be `jobs/application`, `jobs/review` and `jobs/route`. Let's say we have these route in each of the controller in one module. If you look at those route then we can see that it is redundant to write `jobs`. So, there is a option for that in module also. We can do routing module for all modules but it is not feasible. try to avoid it as it isn't not maintainable. 

### Pipes
ParseIntPipe, ParseFloatPipe
### Application Context and Execution Context 
Application context means what kind of application we are running ? 
![[Pasted image 20250117231107.png]]

![[Pasted image 20250117230118.png]]

### Application Lifecycle
There are three phases in application lifecycle. One is Initializing phase, Running phase and Terminating phase.

Initializing phase first involves bootstrapping the program through `main.ts`. After that, each and all module will be initialized as all the dependencies inside them are resolved. As each module is initialized, we can listen for specific event called `onModuleInit` in each module and do whatever is required. As all module are initialized, we can listen for specific event called `onApplicationBootstrap`. We can listen for this event is module or controller or service which is connected to that module. Let's say we have console.log in Service and the module which uses that service. then the `console.log` of the service will run earlier as we know for module to be inititialized, provider must be initialized first

![[Pasted image 20250118120558.png]]
then only we go to running phase and listen for API request.

Terminating phase also has some events which we can listen like `beforeApplicationShutdown` and other modules which we can look above.


> [!NOTE] Title
> `app.close` just close the nest application and not the node.js process. we can also use `app.enableShutdownHooks()`
### Request and Response Flow
From request from, at first Global middleware is run. Then module level middleware phase is used. and then Global Guard Phase is used. after that look at image below. And for everything, there is Exception Filter working for any exception. At first, Request level exception is used and controller level is used and then global level exception is used.
For Response At first Request level Handler is executed, then controller is executed and then Global.
![[Pasted image 20250118124248.png]]
### Middleware
There are two kinds of middleware. They are Functional and Class based middleware. If we need dependency injection, then we need to use Class based middleware. Otherwise Functional middleware is okay. We can write code of functional middleware just like in express but in module we need to register like this 
```js
export const Module implement NestModule {
	configure(consumer: MiddlwareConsumer) {
		consumer.apply(middlewarekoName).forRoutes("kun routes ko lagi")
	}
}
```

Now for class based middleware
```js
export class userAgent implement NestMiddleware {
	use(req: Request, response, next) {
		// we can write code here
	}
}
```
and we can implement it in module just like functional middleware and nestjs will solve whether that thing in functional or class based

But if you look at it, then they look just the same, right ? Class based middleware actually can accept some parameters. Let's say we have `ADMIN` and `USER`. only `ADMIN` was allowed. Now functional middleware can't take any parameter but class based can. For that to happen, we have to inject from providers. So, that we need to write in Module
```js
providers: [
	provide: 
	useValue:
] // this value will be injected in that middleware and use there.
```
in middleware 
```js
export class userAgent implement NestMiddleware {
	constructor(@Options() private userAgent: userAgentType)

	use(req: Request, response, next) {
		// we can write code here
	}
}
```

==Global Middleware==
We can execute it in App Module just like we did it earlier. or we can make that class component functional and do express style.

### Interceptors 
it is inspired from aspect Oriented Programming. That means whenever interceptors are used, we can transform the data before and after the function is executed.
![[Pasted image 20250118134556.png]]
### Environment Variables
We should write this in app module This will look for `.env` in root folder and load it.
```
	imports: [Config.Root()]
``` 
If we want to use the environment values, then we need to use `ConfigService`. For that to happen, we need to import `ConfigModule` and then only can we use `ConfigService`. Let's say we have `JobsModule` and we need to inject `ConfigService` in `JobService`, then we need to import `ConfigModule` in `JobModule`. Now if we need environment variables in multiple place, we might find it tedious. We can make that module global also with options `isGlobal: true`. then, we don't need to import `ConfigModule` everywhere.