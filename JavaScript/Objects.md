## Creating a Object
```
let obj = new Object() // object contructor syntax
let obj = {} // object literal syntax

obj = {
	name: "Bibek",
	"likes food" : true,
}
```
## Adding or Accessing Property in Object
-  we have no limitation in naming property. we can use `for`, `let` or any other things. but we need string. If we use `0`, then it is converted to string `"0"` and we can access also
- A notable feature of objects in JavaScript, compared to many other languages, is that it’s possible to access any property. There will be no error if the property doesn’t exist! Reading a non-existing property just returns `undefined`.
- We can check if property exist or not by using `in`
```
//Accessing Property
console.log(obj.name)
console.logo(obj.["likes food"]) //use bracket syntax for multiworded key

checking whether key is availble or not
console.log("name" in obj) // true as name exist
console.log("blabla" in obj) // blabla doesn't exist

let getInputFromUser = "name"
console.log(obj.[getInputFromUser]) // this gives us great flexibility

//Adding propety
obj.roll = 10 // adding roll key in object
obj.["likes America"] = true // adding "likes America" key in object
```

## Removing Property in Object
```
delete obj.roll
delete obj["likes America"]
```

## Looping in Object
- done via `for..in`

```
for(key in object) {
	console.log(key)
	console.log(object.[key])
}
```

## Object Referencing and Copying
- **When an object variable is copied, the reference is copied, but the object itself is not duplicated.**
- Two objects are equal only if they are the same object. but two independent objects are never same `let a = {}: let b = a: console.log(a == b) // true`. but `let a = {}; let b = {}; console.log(a == b) // false`
- `const` object can be modified. You can't modify it directly like `let obj = newObj // error`. as only object is constant and must point to same reference but it's properties can change. but we can also make properties constant but by using `Property flags and descriptors`.
#### `Object.assign`
- Copying is done by `Object.assign` e.g.`Object.assign(dest(target),...source)`. If the copied property name already exists, it gets overwritten. 
    - `let clone = Object.assign({}, source)`
    - `let clone = Object.assign({}, source1, source2)`
- But `Object.assign` fails if we don't have primitive value in key. In that scenario we need to do `deep cloning`
#### `StructuredClone`
- `let clone = structuredClone(user);
- It can clone most data types like `object`, `arrays` and `primitives` but fails if Object has `functions`. For that we need `_cloneDeep(obj)` from **lodash** library

## [[Garbage Collection]]
## [[`this`]]
