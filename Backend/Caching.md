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
#### Initializing Redis?
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
cllient.hset("spanish", "red", "rojo");

client.hget("spanish", "red", console.log) // it gives null, rojo
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
const util = require("util");
client.get = util.promisify(client.get) // it change the callbacks to async await

const blogs = await client.get(key)// key being user._id
```
3. If not then we do database operations and set the data to frontend.
3. Set the value in redis.

#### Problem in this approach
1. Let's say we add the blog. Even when we add the blog, it will always give the old data as express the cache and send that result. 
2. Code is dirty. Like who want so much cluttered code ? 
![[Pasted image 20241016180312.png]]
