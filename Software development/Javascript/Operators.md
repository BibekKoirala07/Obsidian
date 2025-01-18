- All operators returns the value. universal truth it is

## `typeof()` Operators
```
console.log(2) // number
console.log(Math) // object as it is built in object
console.log(null) // object but it is officially recognized error coming back from early days of javascript
console.log(function) // function
```
## Basic Operators
#### Unary Operators
- having only one operands `let value = +value`
- `+` does Numeric Conversion, it doesn't do anything to `number` but it converts any other to `number`. Basically, it is same as `Number()`
    - `x = +true; console.log( +x )` // 1
    - `x = "" console.log( +x )` // 0
    - `x = "2"; console.log(+x)` // 2
#### Binary Operators
- if it has two operands `let value = x - y`

#### Math(Arithmetic) Operators
1. `+` (Unary)
2. `-`
3. `*`
4. `/`
5. `%`
6. `**` // exponentiation  `2**3 = 2*2*2`
7. `++` // incremental
8. `--` //decremental

##### `++`(incremental), `--`(decremental)
- their precedence is higher than most
- we can have this operator ahead or behind the operators. postfix(`x++`) and prefix(`++x`)
- the only difference between them is the returned value. the returned value of postfix is old value while the returned value of prefix is new value
- `let value = 2 console.log(++x)`, the `++x` increase the value of `x` and returns the increase value. i.e. `3`
- `let value = 2 console.log(x++)`, the `++x` increase the value of `x` and returns the old value. i.e. `2`. but if we check the value of `x` it is `3` now. 
#### String Concatenation with `+`
- if `+` is applied to strings, it merges them. if one of them is `number`, even then, it converts the given expression to `string`
- `console.log("1" + 2) // 12`
- `"1" + 2 + 2 = 122`
- `2 + 2 + "1" = 41`
- `+` is only operator which works like this, other operators like `-` convert any operands to `Number`

#### with other (`-`,`/`)
- `6 - "2" = 4`
- `"6" / "2 = 3`


## Operators Precedence

- If it is harder to learn about table, just remember that unary operators have higher precedence than binary
- `=` is also an operator with precedence of 2. The `x = value` write the `value` into `x` and return it
    - ` a = 1, b = 2; c = 3 - (a = b + 1)`, in this expression, first (`a = b + 1`) is done, meaning value of `a` is calculated and then later performance is done

##### Chaining Operators
- `a = b = c = 2 + 2`, first the operator is calculated from right and left. so first `2+2` is calculated

## Bitwise Operators
- Bitwise operators treat arguments as 32-bit integer numbers and work on the level of their binary representation.

Following are bitwise operators
- AND ( `&` )
- OR ( `|` )
- XOR ( `^` )
- NOT ( `~` )
- LEFT SHIFT ( `<<` )
- RIGHT SHIFT ( `>>` )
- ZERO-FILL RIGHT SHIFT ( `>>>` )
## Comparisons Operators
`>  <  >=  <=  ==  ===  !=  !==`

- All comparison operators return a boolean value:
- `undefined` is incomparable so, it returns `false` always and it shouldn't be compared at all
```
null > 0 //false
null >= 0 //true
null == 0 //false

undefined > 0 //false
undefined < 0 //false
undefined >= 0 //false
```
look below for explanation
#### Comparisons Checks
-  in math conversion or comparison checks `null` is converted to `0` while `undefined` to `NaN` ***but process is different in equality checks***

1. `>`
    - `null > 0` // false as `null` is converted to `0` and given operation is `false`
    - `undefined > 0`, // false `undefined` get converted to `NaN` and we all know any comparison on `NaN` is false so it returns `false`
2. `>=`
    - `null >= 0` // true as `null` is converted to `0` and and `0 >= 0` is `true`
    - `undefined >= 0` // false, as `undefined` gets converted to `NaN`, so `false`
3. `<`
    - `null < 0` // false
4. `<=`
    - `null <= 0` // true

#### Equality Checks
1. **Equality (`==`) :** 
   - Checks if two values are equal. It performs type coercion, so it can compare values of different types. `alert('' == false ); // true`. If we compare `null == undefined // true` as they are of same value but different type
   - `null == 0` , `null` is only equal to `undefined` in value but not in type. so the answer is `false`
   - `null == undefined`, // true
1. **Strict Equality (`===`):** 
   - Checks if two values are equal without performing type coercion. Both the value and data type must match for the comparison to be true.
   - 
2. **Inequality (`!=`):** Checks if two values are not equal. Like the equality operator, it performs type coercion.
3. **Strict Inequality (`!==`):** Checks if two values are not equal without performing type coercion.

## Logical Operators
- `||`, `&&`, `!=`, `??`
#### `OR(||)`
- it returns first truthy value like `console.log(name || "Bibek" || null) // return "Bibek"`.
- `result = value1 || value2 || value3 || "Bibke"` // returns first truthy value or if all value are `falsy`, it return last value.  It convert every operand to boolean value an return first `truthy` in its original format.
- **Short-circuit evaluation** == `alert name || function()`. `function()` is evaluated one and only if `name` is `truthy`. if `name` is `falsy`, the statement doesn't move forward and terminated there or move to new line

#### `AND(&&)`
- returns first `falsy` value just like `||` but in opposite way.

#### `NOR(!)`
- `let value = !value1`. it convert every operand to boolean value and returns the inverse of it.
- using in tricky way `let value = !!value1`.first `(NOT)!` and invert it again to change it to its equivalent boolean value

#### Null-ish Operators `??`
- This is a recent addition to the language. Old browsers may need [polyfills](https://javascript.info/polyfills).
- It treats `null` and `undefined` as same and others value as `defined`
- `a ?? b`. if a is defined then `a` otherwise `b`
- We can rewrite it as `result = ( a !== null && a !==undefined)? a : b `
- We can also make that it returns first defined value i.e. `let user = "Bibek"; alert(user ?? "ramesh")`. It denotes. return user if defined or returns `ramesh` don't if `user` is undefined.
- `||` returns first `truthy` value however `??` returns first `defined` value.


## Comma Operators
- extremely low precedence, lower than `=`
- The comma operator allows us to evaluate several expressions, dividing them with a comma `,`. Each of them is evaluated but only the result of the last one is returned.
```
	let a = (1 + 2, 3 + 4)
	console.log(a) // 7
```
Here, the first expression `1 + 2` is evaluated and its result is thrown away. Then, `3 + 4` is evaluated and returned as the result.