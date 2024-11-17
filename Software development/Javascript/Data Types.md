There are 8 types of data types in JS.
# Primitive Data  Types
- `primitive` - containing only one thing/data

### Methods of Primitives
A special “object wrapper” that provides the extra functionality is created, and then is destroyed. The “object wrappers” are different for each primitive type and are called: `String`, `Number`, `Boolean`, `Symbol` and `BigInt`. Thus, they provide different sets of methods.

```
let str = "Bibek"
console.log(str.toUpperCase());
```
1. The string `str` is a primitive. So in the moment of accessing its property, a special object is created that knows the value of the string, and has useful methods, like `toUpperCase()`.
2. That method runs and returns a new string (shown by `alert`).
3. The special object is destroyed, leaving the primitive `str` alone.

- null/undefined have no methods. The special primitives `null` and `undefined` are exceptions. They have no corresponding “wrapper objects” and provide no methods. In a sense, they are “the most primitive”.
## [[Number]]
- some special values are `Infinity`,`-Infinity` and `NaN` 
- `NaN` is sticky, means any other calculation with it always gives back `NaN`.
- Mathematical Operation is safe in JS, meaning any operation we do like dividing by 0, have non-numeric value as Number will never stop the script. Worst we will get is `NaN`.

## Big Int
`const bigInt = 123456789n`
## [[Strings]]

## Boolean

## `Null` value
- `let value = null`
-  `null` is not a “reference to a non-existing object” or a “null pointer”.
-  It’s just a special value which represents “nothing”, “empty” or “value unknown”.
- the above says that `value` is know known

## Undefined value
- Values that have been declared but assigned are `undefined`
- It makes a type of its own, just like `null`.
- The meaning of `undefined` is “value is not assigned”.
- If a variable is declared, but not assigned, then its value is `undefined`:

```
let age;
alert(age); // shows "undefined"

// technically it is possible to assign the value to undefined
let age = undefined // it is valid
```
# Non-Primitive Data Types
## [[Objects]]