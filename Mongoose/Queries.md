### Sorting

```javascript
await Person.find().sort({ age: -1 }); // descending
await Person.find().sort({ age: 1 }); // ascending 

await Person.find().sort({age: 1, weight: -1}) // return sorting with age but when age is same, it sorts by weight
```

### Query Operators
###### 1. $gt
###### 2. $gte
###### 3. $lt = less than
###### 4. $lte = less than or equal to
###### 5. $ne = not equal to
###### 6. $in = matches any value specified in array
###### 7. $nin = opposite of $in, doesn't matches any value specified in array
###### 8. $eq = equal to
###### 9. $exists = gives document in which specified fields exists
###### 10. $and = gives document in which every condition is fulfilled
###### 11. $or = gives document in which only one condition is fulfilled
###### 12. $not = gives document which is opposite

```javascript
	Users.find({age: { gt: 10 }})
	Users.find({age: { gte: 10 }})
	Users.find({age: { lt: 10 }})
	Users.find({age: { lte: 10 }})
	Users.find({name: { $ne: "ram" }}) // get names not equal to "ram"
	Users.find({name: { $in: ["ram", "shyam"] }}) // get name "ram" and "shyam"
	Users.find({name: { $eq: "ram" }}) // get name equal to "ram"
	Users.find({name: { $nin: ["ram"] }}) // get users whose name not equal to ram
	Users.find({name: { $exists: true }}) // get users whose address exists
	Users.find({ $and : [{ age: { $gt: 23 }}, { status: active }]})
	Users.find({ $or : [{ age: { $gt: 23 }}, { status: active }]})
	
```
