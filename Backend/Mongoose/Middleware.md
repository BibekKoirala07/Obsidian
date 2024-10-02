Website: [ Mongoose Middleware Explained](https://www.ayoubkhial.com/blog/mean-web-app-part-9-mongoose-middleware-explained#what-is-middleware)
### By Category
Thera are two types of hooks by category. They are 
#### Pre-hook
#### Post-hook



Mongoose has 4 kinds of middleware

![[Pasted image 20240602151340.png]]

### 1. Document Middleware
 It handles operations involving single document such as saving, deleting and updating. These hooks are defined on schema level and are executed on document itself. The `this` keyword represent the document itself and `this.constructor` represent the model that operation is being performed. It means the hook don't work on collection level. It only works on document. 
 
```javascript
userSchema.pre('save', function (next) {  

	console.log("this", this) // this represent the document itself
	console.log("this.constructor", this.constructor) // this represent the               model that the operation isn't being executed
	
	next();
});
```

> [!tips] Primarily Used on
> Document: `save`, Document: `create`, Document: `updateOne`, Document: `remove`, `validate` Document: `deleteOne`. as it check before whether the operation is valid or not. `create` is essentially a wrapper on `save()`

### 2. Query Middleware
### 3. Aggregate Middleware
### 4. Model Middleware
