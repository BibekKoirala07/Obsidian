- a function is a special kind of value
- a function must always do only one task and that is it. Don't perform more than one task in one function.
- Functions should be short and do exactly one thing.
- always use local variable, don't use global variable unless required
- the copy of variable is created when data is passed through parameter. if we change the value inside,, the global variable doesn't change cuz the copy is passed and not direct variable
- A parameter is the variable listed inside the parentheses in the function declaration (it’s a declaration time term).
- An argument is the value that is passed to the function when it is called (it’s a call time term).

### Naming a functions
- functions are action. So, it is better to use `verb` to describe it. like `get do create check handle calc`

### Function Declarations
- **A Function Declaration can be called earlier than it is defined.** It is because of algorithm that runs the javascript. As the script is runed, it looks for global function declaration and creates a functions. After that step, the code is executed, so it can be accessed everywhere. However, in `use strict` mode, the function declaration does follow scope and can't be accessed outside of scope
-  a global Function Declaration is visible in the whole script, no matter where it is.
```
function func(name = "Bibek") { // parameters and default value

}
```
- We can have default value as another function too like 
```
function func(name = anotherfunc()) { //passing another function as default parameter
	if default name is given, the default function or text isn't processed at all
}
```
- if function doesn't returns or is empty return, then, it returns `undefined`

### Function Expressions
- **A Function Expression is created when the execution reaches it and is usable only from that moment**
- have a `;` at the end of expression as it is a value
- create a function and store it in `sayHi` variable.
```
let sayHi = function() {
};
console.log(sayHi) // this gives source code
```
- a function is a value, and we should deal with as such
- we can also provide the function in parameter. and they are called anonymous function

## Arrow Function
- shorter version of functional expression