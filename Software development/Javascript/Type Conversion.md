### String Conversion
- It is done, when we need String value
```
let value = String(value) // the type of value is converted to String here
```

### Numeric Conversion
- if the String isn't valid, we get `NaN` as converted value
```
let value = Number(value) // converted to Number here

console.log("6" / "2") // 3 as string is converted to number
```
1. `undefined` -> `NaN
2. `null` -> `0`
3. `true`, `false` -> `1`, `0`
4. any string -> whitespaces(including `/t`, `/n` or spaces) are removed from start or end. If remaining string is empty(` `), it gives `0`. the string is converted to number. If there is error, it gives `NaN`


### Boolean Conversion
- the string with `0` is actually `true`
```
let value = Boolean(value)
```
