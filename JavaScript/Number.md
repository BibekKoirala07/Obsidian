`const num = 1_000_000_000`
`let num = 100000000`

### toString(base)
The method `num.toString(base)` returns a string representation of `num` in the numeral system with the given `base`.
```
let num = 255: 
alert(num.toString(16)); // ff 
alert( num.toString(2));  // 11111111
```

### Global Math Object
`Math.floor`

Rounds down: `3.1` becomes `3`, and `-1.1` becomes `-2`.

`Math.ceil`

Rounds up: `3.1` becomes `4`, and `-1.1` becomes `-1`.

`Math.round`

Rounds to the nearest integer: `3.1` becomes `3`, `3.6` becomes `4`, the middle case: `3.5` rounds up to `4` too.

`Math.max(num1, num2, num3)`
`Math.min(num1, num2, num3)`
`Math.random()
`Math.abs(num)`

### toFixed(n) - returns `string`
```
let value = 12.36.toFixed(1) // 12.4- return string
```

## Testing numbers
```
console.log(isNan("str")) //false, we cann't use NaN === NaN as it always return false
Number.isNaN("str") //false, checks strict typing means type should be number and NaN
console.log(isFinite()) return false if the valeu is NaN/Infinity
Number.isFinite() // type should be number and finite value, any other case it is false
```

There is a special built-in method `Object.is` that compares values like `===`, but is more reliable for two edge cases: 
1. It works with `NaN`: `Object.is(NaN, NaN) === true`, that’s a good thing.
2. Values `0` and `-0` are different: `Object.is(0, -0) === false`, technically that’s correct, because internally the number has a sign bit that may be different even if all other bits are zeroes.

**parseFloat and parseInt**
they read the number from data type until they can't. we can use them to extract the number from currencies
```
console.log(parsefloat('100px')) // 100
console.log(pasefloat(12.5em)) // 12.5
parseInt("12.3") //12
parseInt("12.3.5") // 12.3 , it stops on second comma
parseInt("a123") // NaN as it couldn't extract number
```