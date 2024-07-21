Mongoose has 4 kinds of middleware

![[Pasted image 20240602151340.png]]

### 1. Document Middleware
###### It handles operations involving single document such as saving, deleting and updating. These hooks are defined on schema level and are executed on document itself. The `this` keyword represent the document itself and `this.constructor` represent the model that operation is being performed

```
userSchema.pre('save', function (next) {  

	console.log("this", this) // this represent the document itself
	console.log("this.constructor", this.constructor) // this represent the model       that the operation isn't being executed
	
	next();
});
```
### 2. Model Middleware
### 3. Aggregate Middleware
### 4. Query Middleware
