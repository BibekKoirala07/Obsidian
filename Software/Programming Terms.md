## On the basis of execution label
#### Compiled Language
Converted into machine code before execution using a compiler. Some of the examples are C, C++, Go, Rust, etc.
#### Interpreted Language
It is interpreted line by line. Some of the examples are Js, Python, PHP, Ruby, etc..
#### Hybrid Language
Combine compilation and interpretation. Some of the examples are Java, Python, etc.

### On the basis of Typing
###### Static Typing
Types are checked at compile time. e.g. C, Java, Go. That means types should be written when writing the code or they have to be inferred. And normally types can't change afterwards, at least without Type Conversion
###### Dynamic Typing
Types are checked at runtime. e.g. Python, JavaScript. Have more runtime errors.
###### Weak Typing
Allows implicit type conversion, meaning that one type can be automatically converted to another during operation. eg. Js, PHP. In Js, we can add two different types and js will compute the resulting value and types automatically. This should be avoided.
###### Strong Typing
The operation you can do on variable depends on type. for eg. we can't add string and number. The advantage is that the compiler can do more thorough checking and point for error.

## On the basis of Memory Management
#### Manual Memory Management
Requires the programmer to free the space manually. e.g. C, C++.
#### Automatic Memory Management
Memory is automatically managed at runtime. e.g. Js, Python, Java
## On the basis of Programming Paradigms
#### Imperative and Declarative Programming
Focuses on how should we write the code. Like if there is 100 length of array and we use for loop to do something then it is imperative programming. If we use .map then it is declarative programming. In declarative programming, we just denote what to do and not how to do but in imperative programming we denote what and how to do. Normally Declarative programming provide better developer experience. Some of the example of DP are HTML, CSS, SQL, Prolog and IP are C, Js, Java, Python. In DP, we need to know that system itself decides how will it achieve the result. DP abstract the control flow and just focus on what needs to be done.
#### Procedural Programming(It is a subset of imperative programming)
Programs are collection of functions or procedures that acts on data. e.g. C, Pascal. Here, variable may store the data and it might change overtime. It uses if/else, do while, for loop like things to control the flow of program. Normally they use global state and change that state using functions and give the o/p.
#### Reactive Programming.
It is focused on the fact that there should be reaction to the change of data. e.g. React.js, Kotlin(Coro routines). It happens asynchronously. They work with streams and workables.
#### Event-Driven Programming
The flow is determined by the events like user actions, sensor output or other things. They use event handlers and handlers. It is common in Web development and GUI development. e.g. Node.js, Js(browser based).
#### Functional Programming
It is based on mathematical functions. Don't need to be complex but concept like immutability, pure function(a function should always return same o/p for same i/p), higher order function `h(f(x))` are inspiration taken from mathematics Some of the core concepts of functional programming are
- Pure functions it means producing same o/p for same i/p, like if there is function to add two number it should return same o/p for same i/p.) also no side effect which means it doesn't modify the variable outside the scope, alter data and do I/O operations.
- Immutability(Data can't be changed once created, new variable are created and data is put there. We achieve it in js using const)
- First class Functions(We can assign functions like other variable, we can assign them as other variable) 
- Higher Order Functions(Functions that takes other functions as arguments or return them as result, we can do it in js also)
- Declarative Code(in js we use .map and .filter) and 
- Recursion(Iterations are replaced by recursion) . e.g.. Javascript(partially), Lisp, Haskell.
Advantages are that it is easier to learn. Disadvantages are that it isn't suitable for low-level hardware operation as it is declarative which may allocate more memory or resources when system decides how is implemented.
#### Object Oriented Programming
It organizes the code into object which represent the real world entities. Following are the core principles of OOP:
###### Encapsulation/Privacy: 
It is the process of ensuring data and functions are kept in a single unit of class Just look at the name "capsule" . It has capsule that has everything needed to interact with that object. Thus name "encapsulation". And the main principle is that the access should be controlled so that data can't be modified in random way. It hides data or variable of object from outside world. They can only be accessed through public methods like getters or setters. It prevents misuse. It gives Data Hiding and Modularity. Know about access control also.
```
Class ClassName {
	// initillize public, private and protected data
	
	// Write constructor 
	// It is automatically called when a new object of this class is created. 
	
	// Write setter to update the value of the private property.
	
	// Write getter to retrieve the value of the private property.
}
```
###### Inheritance:
It is an act of inheriting the properties and function from one class to another. Know method overriding also.
```
Class Animal {

}

class Dog extend Animal {

}

var myDog = Dog("Peter");
myDog.makeSound()
```

> [!NOTE] What if child and parent have same methods or variable
> Then, child will override the parent automatically. we can use `super` keyword to call parent method or variable
###### Polymorphism:
It allows different class treated as object of same super class. It means "many more". Or, it refers to ability of different object to respond to same methods in different way. These are some types of polymorphism:
1. Method Overloading (Compile time polymorphism)
The method of child will override the method of parent thus method overloading.
2. Method Overriding (Run time polymorphism)
The suitable method will run depending on the number of parameters.  Dart actually doesn't support it by default.
###### Abstraction/Interface:
It is the process of hiding the implementation details of the object. No need to understand the implementation to use the function of object. It is the process of focusing on essential properties of the object. The idea is we don't need to know how it works. rather we can just use it and get result. At first we provide that this method will exist. Then subclasses are then responsible for providing their own implementation of these methods.

Abstract can have abstract methods and concrete methods which can be used by subclass later. also attributes also. Class can extend from only one abstract class. This limits the 

Interface have multiple inheritance.
```
abstract class shape() { // for other language it is interface instead of abstract.
	abstract void draw(); // must be implementated by subclass
}

class Shape extends Circle() {
	void draw() {	
	}
}
```
#### Scripting Programming
It is a type of programming which automates the other tasks rather than solving other problems. e.g. Js, Python. It provides quick development.
#### Concurrent/Parallel Programming

## SOLID Principles
## HTTP Exception
1. **BadRequestException (400)**  
    The request is malformed or contains invalid parameters.  
    _Example_: Missing required fields in a request payload.
2. **UnauthorizedException (401)**  
    Authentication is required but not provided or invalid.  
    _Example_: Missing or invalid token for protected routes.
3. **ForbiddenException (403)**  
    The user is authenticated but does not have permission to access the resource.  
    _Example_: Attempting to access admin-only endpoints.
4. **NotFoundException (404)**  
    The requested resource does not exist.  
    _Example_: Accessing a nonexistent endpoint or record.
5. **MethodNotAllowedException (405)**  
    The HTTP method used (e.g., GET, POST) is not allowed for the resource.  
    _Example_: Sending a POST request to a GET-only endpoint.
6. **NotAcceptableException (406)**  
    The requested format or media type is not supported.  
    _Example_: Requesting XML format when only JSON is supported.
7. **RequestTimeoutException (408)**  
    The server timed out waiting for the client to send a request.  
    _Example_: Slow internet connection causes a timeout.
8. **ConflictException (409)**  
    The request conflicts with the current state of the resource.  
    _Example_: Attempting to create a user with a duplicate email.
9. **GoneException (410)**  
    The requested resource is no longer available and has been permanently removed.  
    _Example_: Accessing deprecated API endpoints.
10. **PayloadTooLargeException (413)**  
    The request payload exceeds the server's allowed limit.  
    _Example_: Uploading a file larger than the server's maximum size.
11. **UnsupportedMediaTypeException (415)**  
    The server does not support the request's media type.  
    _Example_: Sending data in XML when only JSON is supported.
12. **UnprocessableEntityException (422)**  
    The request is well-formed but semantically invalid.  
    _Example_: Providing an invalid email format during user registration.
13. **PreconditionFailedException (412)**  
    One or more conditions specified in the request headers are not met.  
    _Example_: Conditional requests using `If-Match` headers fail.
---
14. **InternalServerErrorException (500)**  
    A generic error occurs on the server.  
    _Example_: Database crashes or unhandled exceptions.
15. **NotImplementedException (501)**  
    The server does not recognize or support the requested method.  
    _Example_: Calling a feature that hasn't been implemented yet.
16. **BadGatewayException (502)**  
    The server received an invalid response from an upstream server.  
    _Example_: API gateway returns an error from a backend service.
17. **ServiceUnavailableException (503)**  
    The server is temporarily unavailable due to maintenance or overload.  
    _Example_: Scheduled downtime for upgrades.
18. **GatewayTimeoutException (504)**  
    The server did not receive a timely response from an upstream server.  
    _Example_: Backend service takes too long to respond.
19. **HttpVersionNotSupportedException (505)**  
    The server does not support the HTTP version used in the request.  
    _Example_: Using an outdated HTTP protocol version.
## Level of Programming Language 
###### Machine Level
it is the lowest we can go. It just denotes zeroes and ones and sometimes Hexadecimal. and every type of processor or CPU or microprocessors accept its own type of language. Manufacturer write the manual about how those processor accept the commands. Some of those processor are x86, ARM, e.t.c
###### Assembly Level
It has things like `mov` and other commands. If we write the code in assembly language, it assembles the code to machine level(i.e. in zeroes and ones) and that processor process the code. It takes the code and convert the code to machine level code for that processor. Each OS and CPU architecture, they have their own commands.

Assembly -> object file -> Unix executable
###### C/C++
With this language we have access to OS, memory, hardware or network. With it comes more responsibility. It use compiler to assembly language and it use assembler to convert to machine code. We can create OS with it also. Using assembly and C we can almost do anything. Let's say we are building some Photo editing or video editing software, then we need maximum control over the memory we are using. So we create those things with C++.
###### Js/Python
We can't access network and control hard drive. Actually we can't do only some bit of it.

###### JS
We need to convert the js code to machine code ? In assembly language, we used assembler, for C++, we used compiler g++, For Js, we use different engines to convert that code to form of zeroes and ones. At first SpiderMonkey was created as Mozilla to this day uses it. However V8 is more popular. V8 convert code to zeroes and ones directly. while SpiderMonkey covert the code to bytecode and Virtual Machine convert that bytecode to machine code. So for SpiderMonkey there are two layer. And V8 is faster. V8 is written in C++. 

What is ECMA Script? 
It is set of rules that every Js engines must follow. Remember we have different js engines and we need to make sure that the code that we write must have same effect in every browser.

Node.js is C++ application that has V8 embedded into it.