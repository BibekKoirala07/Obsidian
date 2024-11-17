## `if else`
- The `if (…)` statement evaluates the expression in its parentheses and converts the result to a boolean. 
```
if() {

} else if() {

}
```

## Conditional(Ternary) Operators(`?`)
```
let result = name ? value1 : value2
```

## Switch
- break is compulsory, otherwise cases below the correct case all run
- Grouping of case is allowed.
```
switch(x) {
	case 'value1': {
		break;
	}
	case `value2`:
	case `value3`: {
		break;
	}
	default: {
		break;
	}
}
```
- **Type matters**, x checks for strict equality.
