the source code of any program is just text document. So how does our computer can read it and understand it ?. Before that happens, the code must be compiled. The compilation is the process of transforming `.cs` file to the one that computer can understand. In our case, it is `*.dll`. We can't understand that `.dll`.

We can see the some files in bin folder in console application. We can run that program independently. WE can share that program. We also can see `.dll`. It is just our code compile for computer to understand. `.exe` uses `.dll` to give result. removing `.dll` will not work.

Operator Precedence.
implicitly typed and explicitly typed variable.
```
var name = "Bibek";
string name = "Bibek";
```
return for debugging and breakpoints.

methods/function

Exception represent runtime errors.

A solution in C# VS is just collection of project.

string interpolation // kei fancy hoina just use {} in string to add variable 
out keyword needs to be learned
#### Object Oriented Programming.
List, String, Array are all Classes defined in C# library. If they aren't enough, we built our custom type.

###### Absraction
Hiding details from the users. That is it.

==No Logic outside classes are permitted==.

Each date and time is represented as number. like 01.01.2025 is represented as some 64 bit unsigned integers

==Like Undefined, C# has uninitialized. C# can't have uninitialized variable throughout the program at all. It will give uninitialized error. However, if the fields(attribute is variable of class) is uninitialized is zero.==.

Data hiding is done in C# to hide class members especially methods and fields.

Virtually all C# variable or methods should be written inside class. after .NET 6 top level statement was introduced. It was hassle to write everything in class. So we could write anything above the class or at top of the file. Thus the name top level statement. However that is modified to get inside Program class when compiled. So both are same. ==This still remain that all code done in C# must be inside the class. Nothing can be outside the class at all.==.

Encapsulation is different from data hiding. This actually isn't that difficult to understand at all. Combining or Binding fields with methods is called encapsulation. The term is from capsule. The medicine. It describes that data is one part of capsule and methods as another and they are joined in one capsule or class. Most of the time, it is necessary to put the method that operate on some data in same class.

==Remember Data Hiding and Encapsulation isn't same==

Method overloading. multiple methods with same name in different class.

Overloading Constructor. and we can call one constructor from another. we can do that by using `this` keyword. `this` keywords call another constructor from one constructor.
![[Pasted image 20250211120648.png]]
When constructor is called. the below constructor is called and then only upper. In our case second one has no code at all.

Statement doesn't evaluates the value, Express evaluates.

Express bodied methods
![[Pasted image 20250211121011.png]]


`this`

Optional Parameters. should be only compile time constants. Not run time constants. Like we can't write Datetime() in optional parameters as it is run time. If both constructor are same with only one different being having one more optional paramters, then less ambigious constructor is used. Optional parameters must appear after required parameters. And in case of providing second optional parameters, the first optional parameters should be provided. meaning preceding optional parameters should also be provided.

Validation of constructor parameters.

use `nameof` rather than passing variable name.

`const` must be done at declaration while `readonly` can be done at constructor only. but remember there are limitation with it as we can't change anything after that. const demand compile time constant value. We can't use var as var does run time checking. const should start with capital letter.

Limitation of public fields, need to properties. getter and setter are called properties.

difference between fields and properties.
![[Pasted image 20250211130607.png]]


Object Initalizer.
![[Pasted image 20250211142247.png]]
We can use object Initializer if only fields are public. It runs after constructor. 
But setting fields to public is concerning. So to mitigate that C# 9 introduced `init` features to make sure  we can assign the value of fields only during object construction. after that it will act like there was no setter at all. ![[Pasted image 20250211142906.png]]
Avoid using Object initializer.
try to use constructor more than object initializer.

Computed property
It is the case when we need the custom fields. Like what if we need our length and breadth attached to some string ? 
![[Pasted image 20250211143329.png]]
Don't try to do heavy calculation if you are just doing getting the value. first one is expression bodied method while second one is computer properties.
properties and methods are computed everytime they are called. this is not so wanted thing. The general rule is Fast thing can be Property while slow can be methods. In general methods represent actions while property represent data.

All const fields are implicitly static. When the method is static. it belongs to a class as a whole. not in a instance. If all the methods are static then we can make class static. static class can't be instantiated. It only works as a container for methods. Static classes can only contain static methods. Can't add non static method to static class at all. but opposite can be true.


Both static and non static classes can contain static methods and properties.

static constructor are just like regular constructor but don't have access modifier. instead it has static modifier.
![[Pasted image 20250211152919.png]] 
both the initialization of the static fields and calling of the static constructor happens before first instance of containing class is created. Unfortunately we don't know when exactly that happens as it is closed by very low level implementation by .NET. So, the value stored in `_firstUsed` is not precise.
==Don't' need to use static fields and properties. Avoid it as much as possible.==

==Single Responsibility Principle==.
One of the solid principle. A class should be responsible for only one thing. A class should have only one reason to change. Look at this ![[Pasted image 20250211154825.png]]
We can see it exposes different methods. right ? But the goal of List is only one thing. So show the dynamic variables.

Repository are classes and components that encapsulate the logic required to access data sources like database, file, etc.

Namespaces declare a scope that contains a set of related types. We have global and local namespaces.
If we don't describe namespace. then that class goes to global namespace.
![[Pasted image 20250211170638.png]]

we should use file scoped namespace declaration. was form C# 10

using Global Directive
we can import globally also. The restriction is that global using must be above non global ones. 
We can use `Console`class without using its `namespace` `using System`.

The solution to that mystery is that there are some global using defined for application and the `System` namespace is among them. They were added automatically when new `Console` application was built. we can check that in `obj/debug/net`. if you look there, some application not created by programmer have `g` letter before `.cs`.

we can use global import using in different file


==Creating random number is pain in the ass in C#==.
As it is determined by seed value. that see value is taken in constructor. If not provided it is determined by tick value where tick = 100 nano second from the time system starts.  means if for two object is made at two identical time. the random numbers generated are going to be same. So. take care of that. The best approach is to only generate only one random number in entire program.

==Dependency should be given through constructor rather than initializing it==.
![[Pasted image 20250211190809.png]]
learn enums also.
![[Pasted image 20250211193743.png]]
In this case it works as Enums are integers under the hood. but don't try to overuse it.
### Object Oriented Properties Advanced on C#

Single Responsibility Principle.

System.Object is base class for all the objects in C#.

Implicit and Explicit Casting happens whether the conversion is safe and lossless.

Upcasting and Down casting. If we want to use down casting and are sure that operation will succeed. then use down casting. Down casting requires explicit casting. In most cases it is serious problem to use Down casting and using it indicates some serious problem with design of problem.

is Operator is used to check if some object is of given type.

Null means a variable or fields doesn't hold any instance. Null value is common for all classes. Default value can't be null. That is the reason why you see values being initialized to 0 or some random thing.

as operator
Alternative to explicit casting. In EC, like
```Csharp
	Cheddar cheddar = (Cheddar) ingredient;
	// explicit typing means if ingredient is of other types then runtime error will occur

	Cheddar cheddar = ingredients as Cheddar; 
	// it will return null if casting fails. Only works to cast to nullable types. as result can be null, it only works on those operator to cast to those that can be assigned null

int number  = ingredients as int
// it will naturally fail as int can't be null. 
```
as don't give runtime error.

Abstract class
How to prevent a class from being instantiated. Abstract class can't be instantiated. They only server as base classes for others, more concrete types.

Abstract methods and property
They don't have any implementation so bodies are empty. All Abstract methods are implicitly virtual. So we can override them in non-abstract derived classes. If we use some abstract method named `Prepare` in abstract class then we have to override it in any base class derived from the abstract class.

A need for abstract methods

Sealed class and Methods
Prevent class from being inherited. only overridden methods can be sealed. It is the way of saying that I am overriding virtual types and it ends here. In programming it isn't used. However, in C# libraries, it is frequently used. Like in `string` class in C#.
like here.
```
public class MyBetterString: string {} // incorrect as we can't extend sealed class.
```
However sealing a class does have a problem. It means no specialized version of this class can be created. It also conflict with testing. Most of the times, sealing shouldn't be done.

Static methods are always sealed. all static classes are implicitly sealed.

Extension Methods
add methods of existing types without modifying source type. Like we want to add number of lines used in string(not number of words). then there is no library right ? the solution is extension methods. Extension methods can only be defined in static class and extension method itself should always be static . The parameters of extended type must always be the first parameter. It is good practice to have each class for each extended type. 
After adding `CountLines()` function, we can add as if it was already present in `string` type. We can't modify string type in any way.

A need for interfaces.
If something aren't connected, we use interface. We normally use like `IFlyable`. Interface initialize the method and child will implement it later. The derived method should be public. The method defined in interface are always implicitly virtual so are methods implemented in child. Interface can't be instantiated. This is similarity between abstract class. Like inheritance from two classes in not allowed but from two interface is allowed. 99% of the cases interface contains declarations of methods and properties which is more like methods than fields.

The similarity between abstract class and interface is source of confusion. There are some differences.
Interface defines the set of operations that the implementing type must provide. It doesn't provide any implementation on its own and it doesn't contains any data. 
Abstract class is general blueprint for derived class. It may provide implementation in non abstract methods. Can also have abstract methods. They can contain data.

They both can't be instantiated.
Interface is abstraction over behavior. AB is over likeness. 
Interface defines what can object can do. AB is
Interface's child share behavior. AB belong to general category of things.
Interface example is collection of bird, kite, plane. No similarity except flying. AB for dog, cats, snake as Animal.
Interface is verb, AB is noun
Interface make sure that they can do. AB make sure what they are.
![[Pasted image 20250209194850.png]]
![[Pasted image 20250209195022.png]]
### Exceptions
Exceptions are runtime errors.
exceptions are object in C#. All exceptions are derived from `System. Exception` base class. Like `System.FormatException`. 
We can handle exception our own way by creating classes derived form `System.Exception` class.
![[Pasted image 20250209201004.png]]
Stack trace is the track of all methods that have been called leading to a particular point in the program execution.

Just handle every possible exception. If there is exception in your app. It is just not nice.

Exception are generally Unexpected. Like `OutOfMemoryException` or `NullReferenceException`. They are hard to predict. 
Don't use `try catch` too much. But generally we must define one global `try catch` and some rare local `try catch` that is it.
`finally` is used to clean up resources like `database connection`

multiple `catch` block are allowed. The order of Exception does matter. Care about that. From specific to generic exception.

Throwing Exception Explicitly
It helps to not return the value from method even if method has return type.

### LINQ

