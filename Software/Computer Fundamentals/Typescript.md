### Basics (if not already strong)
- Learn `string`, `number`, `boolean`, `null`, `undefined`
- Learn `any`, `unknown`, `never`
- Learn `interface` vs `type`
- Learn function type annotations
- Learn array and tuple types
- Learn enums

### 🟡 Intermediate

- Learn union and intersection types
    
- Learn type narrowing and control flow analysis
    
- Learn optional properties and optional chaining (`?.`)
    
- Learn `readonly` and `const` assertions
    
- Learn literal types (`"success" | "error"`)
    
- Learn type aliases and recursive types
    
- Learn `typeof`, `keyof`, `in` operators
    
- Learn `as` casting and type assertions
    
- Learn type guards and custom type guards
    

---

### 🔵 Advanced

- Learn generics (`<T>`)
    
- Learn generic constraints (`<T extends X>`)
    
- Learn `Partial<T>`, `Required<T>`, `Readonly<T>`, `Pick<T, K>`, `Omit<T, K>`
    
- Learn `Record<K, T>` and `Exclude<T, U>`, `Extract<T, U>`, `NonNullable<T>`
    
- Learn `ReturnType<T>`, `Parameters<T>`, `ConstructorParameters<T>`, `InstanceType<T>`
    
- Learn conditional types (`T extends U ? X : Y`)
    
- Learn distributive conditional types
    
- Learn infer keyword (`infer U`)
    
- Learn mapped types
    
- Learn template literal types (`${T}-id`)
    
- Learn function overloads
    
- Learn declaration merging
    
- Learn discriminated unions
    

---

### 🧰 Tooling & Ecosystem

- Learn `tsconfig.json` deeply
- Learn strict mode (`strict`, `strictNullChecks`, etc.)
- Learn module resolution
- Learn how declaration files (`.d.ts`) work
- Learn how to write ambient types
- Learn working with DefinitelyTyped (`@types/*`)
- Learn how to use and author types for libraries
- Learn advanced editor tooling with VS Code + TypeScript
    

---

### 🧠 Design & Architecture

- Learn type-safe API clients (e.g., Axios with generics)
    
- Learn schema validation + type inference (e.g., `zod`)
    
- Learn safe pattern design (e.g., builder pattern with types)
    
- Learn complex DTOs and contracts (e.g., GraphQL/NestJS)
    
- Learn how to model domain logic with types
    
- Learn exhaustive type checking
    
- Learn how to structure large TS codebases
    
- Learn dependency injection typing (e.g., NestJS)
    
- Learn how to avoid overengineering with types

### Simple types (Primitive types in js)
There are two ways of write types. Explicit and Implicit way. Explicit way `let name: string = "Bibek"`. Implicit way `let name = "Bibek"`. In implicit way, type is guessed based on assigned value. The process is called inferring. Implicit assignment forces TypeScript to **infer** the value. Sometimes typescript is unable to infer the value. That time it infer to any type. This behavior can be disable using tsconfig.json by adding `noImplicitAny`
### All types
The types are Array, Tuples, Objects, Aliases and Interface, Enums, Union Type, Functions, Casting, e.t.c
```ts
	// Casting // normally casting is done to unknown value. don't do other types as it may cause error
	let x : unknown = "Bibek"
	console.log((x as string).length) // rather than memorizing lot of things, just do it on unknown 
	
	// Array
	const names: readonly string[] = []; // it means array of string. there is option for readonly also

	// tuples
	let ourTuple: [number, boolean, string];
	ourTuple = [false, 'Coding God was mistaken', 5]; // incorrect as it is not in same
	ourTuple.push('Something new and wrong');   // ironically it is correct as tuples don't check                                                         after predefined length

	// Objects
	const cars : {name: string, price?: number} = {
		name: "Toyota",
		price: 2000
	}

	// Functions
	function getNumber(a: number, b: string = "example") : number or void { 
		// if no return type is declared, ts will infer from code. if no return is mentioned, it will             be any

	}

	// Aliases // aliases work on all things while interface work on objects only
	type typedName = {
	  year: CarYear,  
	  type: CarType,  
	  model: CarModel
	}

	// Interfaces // they can extends also // interface only work with objects
	interface interfaceName {
		// define types
	}
	
	interface anotherInterfaceName extends interfaceName {
		// define another types
	}

	// Union Types
	type code: string | number
	let arr: code = "" // Here no method for string or number like toUpperCase is allowed as it isn't                            of type string, rather of type string and number 

```
Utility types like Required, Partial, record are available but there isn't need to know them It just cause information overload.
### Arrays, Tuples
If no types are mentioned, types is inferred. If it seems to be array of number it will be `number[]` and same for strings. With tuples, it is about typed array with predefined length and types for each index. Tuples are great because they allow each element in the array to be a known type of value. Orders matters in tuples. In react, useState return tuple of a value and function. 
### Special types(any, unknown)
###### any type
It disable type checking and effectively allows any type to be used. Mostly, avoid it. If you use it autocomplete will also not work so. It is just like plain js. Let's say we assign number to variable and check for methods available in string type, with any it doesn't cause compile time error but only run time error. just like in js.
###### unknown type
just like any type but we need to write its type later to change that value. Prevents careless mistakes like calling a method that doesn't exist for the actual type. However, it is not unsafe like any type. It forces us to declare type for that variable before doing manipulation. For example there is a content inside box and we want to interact. First we need to know the type of that content. So type narrowing is necessary. This is achieved using unknown type. It is used for ==API calls ==.
###### never type
It represent the value that should **never happen**. Just like function never returning. You can't assign any value to **never** type. It is for the condition where we denote something shouldn't happen at all. It is used in situation which shouldn't happen at all. It is more confusing. Lets say we have three type such as dog, cat and cow. If we get fish, then we must get error. right ? it is achieved using never type.
```ts
function throwError(message: string): never {
  throw new Error(message); // This function doesn't return anything. It just throws an error.
}
```
###### undefined and null
They are already in js. null means the there is nothing in that variable and undefined means the variable isn't defined at all. might have missed to define it.
### tsconfig.json
```ts
{

}
```