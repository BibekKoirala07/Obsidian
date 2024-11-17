[Object methods, "this" (javascript.info)](https://javascript.info/object-methods)

### this has not bound
`this` has not bound in js. It can be used anywhere in js. suppose
```
function sayHi() { 
	alert(this.name );  // this is not incorrect. Here, name is evaluated at run-time                               at run time
}
```

- Arrow function don't have `this` function


## Constructor function