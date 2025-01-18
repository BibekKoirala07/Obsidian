# Introduction

JavaScript runs on every devices which have special program called **the javascript engine**. The browser has an embedded engine sometimes called a “JavaScript virtual machine”. Different engine have different codename. e.g.
> V8 for edge, chrome and opera
> Spider-Monkey for fire-fox
> Squirrel-Fish for safari

If the features is supported in given engine, then chances are that feature is supported in those browser with that engine.

**How do Engines Work** 
- The engine read("parses") the script.
- then compiles the script to machine code
- then machine code runs, pretty fast

### Features
- **High Level Language**, means it provide abstractions for developer to make it easy to work
- **Interpreted Language**, source code is executed by an interpreter at runtime, rather than being compiled into machine code or an intermediate bytecode that is executed by a virtual machine.
- **Dynamic Typing**, variable can changes types during runtime as developer wants it
- **Weakly Typed**, may perform type coercion if needed 
- **Single Threaded**, executes one operation at a time
- **Cross- Platform**, platform-independent meaning can run on various platform i.e. frontend, Server, Mobile Development


### Script tag

```
<script src="" > </script> // type and language are legacy attribute
```

Always use External Script cause it helps in caching. JS is downloaded once and after that only other things are downloaded. If we put it inside, JS is downloaded everytime which make it slow.
If we use `src` then, code inside script doesn't work.

```
<script src="index.js">
	// here js code doesn't work, we have two use two script tags
</script>
```

### Rules while Writing

- While we can get away with writing semicolon `;`, we shouldn't do it. It only works in incomplete expression.
- Our Eyes scan code vertically, so don't write too long horizontal code. Max amount must be 120
- Nested Comments aren't supported. There is tricky way to get away from it. but don't do it. Comments increase the overall code footprint, but that’s not a problem at all. There are many tools which minify code before publishing to a production server.

### Use Strict

- It is to make code work in modern way. However, there is no need to include it if we use ES6 features like modules, classes, etc. in any part of our script as it include `use strict` automatically at top of code

### Variable

- Name should start with alphabets or _  or $ and first character shouldn't be digits.
- camelCase should be used
- Other language like Chinese is also supported but not recommended
- reserved words not allowed.
- In old times, before `use strict`, it was possible to assign the variable without declaring. It would automatically declare the variable

**Concept to know** : 
- JS supports some of the pure functional language features like

### Constant
- Upper-Case Constant - to declare the constant which we already know like "COLOR_RED", "PORT".
- Lower-Case constant - to declare the constant which we don't know and will be calculated in runtime 
### Data Types
### Operators
### Type Conversion
### Conditional Branching
### Looping
### Functions

