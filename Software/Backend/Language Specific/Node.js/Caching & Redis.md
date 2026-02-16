### Query Performance

![[Pasted image 20240617110645.png]]

 Whenever we execute the mongo Query like `Blog.findOne({ _id: req.body._id })`, it send the request to mongo DB. Before the collection, we have `index`. The `index` is a efficient data structure for looking up for data in mongoDB. They are matched up with individual collection. It allows us to be directly in line with data that is to be fetched. Like we have index for `_id`, i.e. when we search for something using `_id`, we don't need to search other documents at all. We can directly go that document and fetch it. How Easy !!!! So, `index` allows us to go directly to the data we care about
 
![[Pasted image 20240617113703.png]]

 Whenever the index is created for collection, it targets the individual property that exist on those records. But we can target multiple property together or consider different property also . Let's say we have indexing for `_id` property. That means if we ever asks for blog post for some `_id`, DB can easily get that blog post quickly as we have indexing for `_id`. If we query using `title`, and if there is no such property in indexing, then we don't enjoy the privilege of fast query. We have to resort back to `full collection scan` and will take time. There are two ways to avoid this.
 1. First Way is to add in index for given field i.e. `title` in shown below . However it reduces the write ability in collection and we may require to query randomly many times in any app where index property isn't used at all like `content`. Like we may need to query using `content`. in this condition, we will do full collection scan. Excessive indexing consume more diskspace and memory. So pain in the ass in the time of deployment as it may bankrupt me and my service providers. Also we may have more than 30, 40 properties. so we may not know which property to index. That is disaster. So, it is not attractive solution![[Pasted image 20241016160243.png]]
 2. Another way to by setting up cache server.

### How does query works 
##### Normal Query
![[Pasted image 20240617130301.png]]
##### Query with Cache Server
![[Pasted image 20240617130326.png]]
 When we make a query from mongoose, then at first, we check for cache server if that exact query been executed before. If it hasn't then mongoDB execute it and result of that query is stored in cache server. Now, Anytime query is executed, same thing is repeated

- **When query doesn't hit and miss cache server**
![[Pasted image 20240617153601.png]]
  After query is ended as that query is added to Cache Server
![[Pasted image 20240617153623.png]]

> [!important] Extremely Important
> Caching Server is only used for Reading Purpose. Not for writing

### Caching with redis
![[Pasted image 20241016162305.png]]
It is a in memory data store. It is key-value pair database.
Tips:  
1. It runs on memory of our machine.
2. It allows us to read and write very very quickly.
3. As it runs on memory, if the program is restarted or turned off and what ever happens.
4. We only store insensitive data in Redis. As redis delete all data after it is turned off.
5. Documentation of Redis is very poor.
#### Initializing Redis
```javascript
const redis = require("redis")

// now we need url which is default for redi
const redisUrl = `redis://127.0.0.1:639` // it is always going to be same

const client = redis.createClient(redisUrl); // client is big object. it has different function for us to manipulate it

// now we can set the value using set function in client
client.set("hi", "there") // the value is set

// retrieve the data
client.get("hi", (err, value) => console.log(value));
client.get("hi", console.log) // it console.log key value of err and value
```

###### For Nested Value
It is just like JSON values.
![[Pasted image 20241016165402.png]]
```javascript
cllient.hset("spanish", "red", "rojo"); // h stands for hash

client.hget("spanish", "red", console.log) // it gives null, rojo for error and value
```

> [!NOTE] One Gotcha
> Redis doesn't store Javascript Object. We rather need to turn that Object into String and store in redis.
#### How does it work
As it works on key value pair. The question is what to put in key that is consistent and unique. We need to make sure every user get different result in different collection.
Let's have this query as example:
```javascript
app.get("", async () => {
	const result = await Blog.find({ _id: req.user._id });
	const tweets = await Tweet.find({ _id: req.uer._id });
})
```
We need to have unique blogs for each user. There are two unique things in above code. They are `_id` for each user and `Blog` collection. combining those we can get consistent and unique value for our redis key.
### Standard Workflow in Redis
1. First we check if the value is in redis or not.
2. If the value is in redis, we return data to frontend. However every redis operation in `async` operation. we don't get result immediately. At its raw code redis doesn't accept `async await` syntax and we need to modify our code for it to accept it by using `promisify` from `utils` node module library.
```javascript
const getBlogs = async (req, res) =. {
	const redis = require("redis");
	const redisUrl = 'redis://';
	const client = redis.createClent(redisUrl);

	const util = require("util");
	client.get = util.promisify(client.get) // it change the callbacks to async await otherwise client.get only accepts callbacks and await isn't possible there

	// Do we have any cached data ? 
	const cachedBlogs = await client.get(userId)// key being user._id
	// if we have cached data return it by converting to JSON
	if(cachedBlogs) return cachedBlogs;

	// if no, we need to respond to request and update our cached to store the data
	const blogs = await Blog.find({ _id: userId })
	return blogs

	// now store the data by stringify them
	client.set(userId, JSON.stringify(blogs));
	 
	}
```
3. If not, then we do database operations and set the data to frontend.
3. Set the value in redis.
#### Problem in this approach
1. Let's say we add the blog. Even when we add the blog, it will always give the old data as redis sends the cached data and send that result. Till now we just did it for get only.
2. Code is dirty. Like who want so much cluttered code ?  and just look at code above this. We can't do this everytime in any blogs. Can we ?
![[Pasted image 20241016180312 1.png]]
The solution with problem explained
###### 1. using exec()
```node.js
const query = Person.find({ _id: userId}); // it just write the query that is it. it doesn't trigger the database search

// Here we check for caching

// here we need to 
query.exec = function() {
	// check if query has already been executred and return
	const result = client.get("query key");
	if(result) return result

	// otherwise isssue the query as normal
	const result runTheOriginalExecFunction(); // it goes and request to mongo

	return  result;
	// then save that value in redis
	client.set("key", result)
}

query.exec((err, res) => {}) // only then mongoose execute the query

query.then((result) => {} ) // .then already uses .exec
```
###### 2. expiration
```
client.set("color", "red", "EX", no of seconds after this value with expires);
```
###### 3. Change
If we concentrate, then we can see that just slightly changing that query will cause entire redis to not function

To change this things, we will write all the logic inside `./services/cache.js` and we will patch the library. Patching means going to overwrite that function. That means going to source code and injecting required function which wasn't present when it was used normally. 
```node.js
const mongoose = require("mongoose");

const exec = mongoose.Query.Prototype.exec; // this store the reference to original exec function. The untouched copy. the copy that is supposed to be executed when any time query is executed.

// we will add in additional logic now. it is called patching
mongoose.Query.Prototype.exec = function() {
    // don't use arrow function as it messes up this. we want this to point to Query.
	console.log("We are patching"); // 
	return exec.apply(this, arguments); // if we run then we will get the log in console. here we patched one console.log();             arguments are arguments that are passed in exec. we use apply to pass that.
}
```
Here we patched the library. So we can intercept the query and check for caching there if that exact query as already been done before. If we import `cache.js` in `index.js`, then we can see that console.log before every query execution. So we are going to use redis here. if we use `console.log(this)` inside this prototype then we can see the query that is going to be done in `object` data type. 
```javascript
const mongoose = require("mongoose");

const exec = mongoose.Query.Prototype.exec;

mongoose.Query.Prototype.exec = function() {
	console.log("We are patching");
	console.log(this.getQuery()); // this means the query that is being exectured and getQuery gives us object for that query
	return exec.apply(this, arguments); 
}
```
But still the query isn't unique. The ids are going to be unique but still query isn't unique. we can look for different collection using same query. The goal is to introduce   name also.
```javascript
console.log(this.mongoosecollection.nama);
```
Now the query is going to be unique.
![[Pasted image 20241028183235.png]]
So we are going to make key for redis.
```javascript
mongoose.Query.Prototype.exec = function() {
	console.log(this.getQuery()); // this means the query that is being exectured and getQuery gives us object. 
	// this is the query instance. this refers to Blog.find({}) or any other query.
	console.log(this.mongoosecollection.name);

	// the goal is to make key using getQuery and collectionname but we can't directly modify getQuery. in js We need to use Object.assign to create anther object.
	const key = Object.assign({}, this.getQuery(), {collections: this.mongooosecollection.name});
	return exec.apply(this, arguments); 
}
```

So the final function that is in `cache.js` is
```javascript
const mongoose = require("mongoose");
const redis = require("redis");
const redisUrl = 'redis://';
const client = redis.createClent(redisUrl);

const util = require("util");
client.get = util.promisify(client.get) 

const exec = mongoose.Query.Prototype.exec;

mongoose.Query.Prototype.exec = async function() {
	const key = JSON.stringify(Object.assign({}, this.getQuery(), {collections: this.mongooosecollection.name}));
	// if we have key value in redis send, if we don't run the query to database and save in redis
	const blogs = await client.get(key)
	if(blogs) return JSON.parse(blogs);
	// else execute the query and store in reslut and save it in redis
	const result = await exec.apply(this, arguments); // the exec here returns model instace and not an object like normal
	// we need to be extremely wary of that as we try to patch the library we need to understand that library might return
	// another type as we are manipulating the library. and we can only store strings in redis 

	client.set(key, JSON.stringify(result));
	return result;
}
```
However this will still not run. Why ? remember I told that we need to return mongoose model Instance which has following properties like `get`, `set` and others but out type is just `JSON` so it doesn't has that. but our exec expect to return mongoose model. So there is problem ![[Pasted image 20241028190201.png]]
So we need to change cachedBlogs to mognoose Model instance.
```javascript
if(blogs) {
	const result = new this.model(JSON.parse(blogs));
	return result;
	// here this refers to the model of the data that is going to be executed. 
	// it is same as new Blog({_id: req.user.id, content: "damn"}); Blog is this.model
}
```
But still it will not work fully. Why ? cuz we have only made a logic for the blog to be just objects. What if blogs is array ? it fails to change to mongoose instance. How to do that ? 
We need to change every object that is inside Array to change to object. So it is easy
```js
if(blogs) {
	const result = JSON.parse(blogs);
	const isArray = Array.isArray(result);
	// we are just checking if the shit is array or not and returning that shit accordingly
	if(isArray) {
		return result.map((d) => new this.model(d));
	} else {
		return new this.model(result)
	}
}
```
Now think of this. Like we are caching every time request is made as we import `cache.js` in `index.js` by
```js
require("cache.js");
```
The problem is that caching is expensive. We wouldn't want to cache when 
 - Data is too much like big object
 - just generally, we don't want to cache everything as cache provider in production is extremely expensive. So cache as less as much you can.

The goal now is to make function which automatically cache if only we only it by mentioning it in tails of query.
```js
const blogs = await Blogs.find({}).cache();
```
What to do when cache is called ? 
```js
mongoose.Query.Prototype.cache = function() {
	this.useCache = true; // so this is how we know cache has been called or not.
}
```
Now we know we have cache and exec function in same query instance . Same query instance is being reference from cache and exec, so we can use useCache in exec. Now if useCache is set we can run the caching logic if not directly run the exec function. So it goes like this.
```js
mongoose.Query.Prototype.cache = function() {
	this.useCache = true; 
	return this; // this make .cache chainable like limit, sort
}

mongoose.Query.Prototype.exec = async function() {
	if(!useCache) {
		return exec.app(this, arguments);
	}
	const key = JSON.stringify(Object.assign({}, this.getQuery(), {collections: this.mongooosecollection.name}));

	const blogs = await client.get(key)
	if(blogs) return JSON.parse(blogs);
	const result = await exec.apply(this, arguments)
	client.set(key, JSON.stringify(result), "EX", 4); // this set the expiry
	return result;
}

```
#### Cache Expiry
There are many way to make the cache expiry possible. that `EX` one isn't great. One prominent example is by ![[Pasted image 20241028200720.png]]
Grouping the data with userId as key. If the user changes the data then, we can delete entire key. So that request has to go to database again for data. this is one way but remember what if user1 add blogs ? remember user2 also can see blogs. should the blog data for user 2 change ? So, the above described way only works for some.

So this is the way.
We need to make the top level hash key more dynamic. Right now we have userId, but it isn't dynamic. For that we need cache function to provide top level hash key from the controllers
```js
const promisfy = requier("utils");
client.hget = promisify(client.hget) // above this we had hset

mongoose.Query.Prototype.cache = function(options = {}) {
	this.useCache = true; 
	this.hashKey = JSON.stringify(options.key || "") // stringigy the key if we get another form.
	return this; // this make .cache chainable like limit, sort
}

mongoose.Query.Prototype.exec = async function() {
	if(!useCache) {
		return exec.app(this, arguments);
	}
	const key = JSON.stringify(Object.assign({}, this.getQuery(), { collections:                  this.mongooosecollection.name}));

	const blogs = await client.hget(this.hashKey,key)
	if(blogs) {
		const result = JSON.parse(blogs);
		const isArray = Array.isArray(result);
		if(isArray) {
			return result.map((d) => new this.model(d));
		} else {
			return new this.model(result)
		}
	}
	const result = await exec.apply(this, arguments)
	client.hset(this.hashkey,key, JSON.stringify(result), "EX", 4); // this set the expiry
	return result;
}
```
By doing this it does work as we have main hashkey as empty string but if we want to add something then
```js
	Blogs.find().cache({ options: userId });
```
So now we need to make sure that value automatically goes away when users add or patch the blogs. For that we will create a clearHash() in the same file as redis implementation and send it to the place where we want to implement it
```js
// above code

module.exports = {
	clearHash(hashKey) {
		client.del(JSON.stringify(hashKey)) // stringify cuz who know what type hashKey is of
	}
}
```
and call that function in 
```js
try {
 // after the blog is added call clearHash
} catch {

}
```
but doing this is so tedious. So we would use the middleware but it would run after the request is finished. Damn, How will that work ? 
and make the new folder there we would use controller and after that controller is finished 
```js
const {clearHash} = require("")
const cleanHastWithMiddleware = async (req, res, next) => {
		await next();
		clearHash();
}
```
### To do 
## You Can Learn Redis with Small Projects Like:

### 1. **Authentication Token Store**

- Store refresh tokens per user.
    
- Invalidate sessions on logout.
    
- Learn: strings, TTL, key expiration, token rotation.
    

### 2. **Simple Caching**

- Cache DB queries or external API results.
    
- Learn: caching strategies, TTL, cache invalidation.
    

### 3. **Rate Limiting**

- Limit requests per user/IP using Redis counters.
    
- Learn: increment operations, expiry, atomic commands.
    

### 4. **Pub/Sub**

- Chat app or notification system.
    
- Learn: channels, pub/sub events, scaling across processes.
    

### 5. **Job Queue with Bull or BullMQ**

- Background jobs (e.g. email sending).
    
- Learn: queues, retries, workers, concurrency.
    

### 6. **Leaderboard**

- Build a game leaderboard using sorted sets.
    
- Learn: ZADD, ZRANGE, scores, ranking.
    

### 7. **Session Store**

- Store user session data in Redis (e.g. shopping cart).
    
- Learn: hash type, session expiration.
    

---

## 🧠 Redis Core Concepts to Learn (No Big Project Needed)

| Concept      | Learn With…                        |
| ------------ | ---------------------------------- |
| TTL & Expiry | Token cache or session store       |
| Strings      | Store token or short-term data     |
| Lists        | Chat messages                      |
| Sets         | Unique users for an event          |
| Sorted Sets  | Leaderboard                        |
| Hashes       | Store user session or cart details |
| Pub/Sub      | Real-time notifications            |