### Schema & Models
1. Every ORM's has type like in mongoose 
Just type give me mongoose schema with this or that and it will give you that.
### Middleware
By definition there are two types of hook. they are `pre` and `post` hook. 
![[Pasted image 20240602151340.png]]
Use AI to generate code rather than to write here.

###### Query Operators
```javascript
// Find users with age greater than 10
Users.find({ age: { $gt: 10 } });

// Find users with age greater than or equal to 10
Users.find({ age: { $gte: 10 } });

// Find users with age less than 10
Users.find({ age: { $lt: 10 } });

// Find users with age less than or equal to 10
Users.find({ age: { $lte: 10 } });

// Find users whose name is not "Ram"
Users.find({ name: { $ne: "Ram" } });

// Find users whose name is either "Ram" or "Shyam"
Users.find({ name: { $in: ["Ram", "Shyam"] } });

// Find users whose name equals "Ram"
Users.find({ name: { $eq: "Ram" } });

// Find users whose name is not "Ram"
Users.find({ name: { $nin: ["Ram"] } });

// Find users where the 'name' field exists
Users.find({ name: { $exists: true } });

// Find users who are over 23 years old AND have an active status
Users.find({ $and: [{ age: { $gt: 23 } }, { status: "active" }] });

// Find users who are over 23 years old OR have an active status
Users.find({ $or: [{ age: { $gt: 23 } }, { status: "active" }] });

// Find users where the age is not greater than 30
Users.find({ age: { $not: { $gt: 30 } } });
```
###### Sorting/Limiting
```javascript
await Person.find().sort({ age: -1 }); // descending
await Person.find().sort({ age: 1 }); // ascending 

await Person.find().sort({age: 1, weight: -1}) // return sorting with age but when age is same, it sorts by weight
```