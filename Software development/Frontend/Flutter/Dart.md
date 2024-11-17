## Data Types

#### Primitive Types
```
	int x = 5;
	double value = 3.14;
	String example = "real";
	bool isString = false;
	String? name = null; // represent the absence of value
	print(name ?? 'Unknown'); // Unknown
```
Tips: 
1. Both `int` and `double` are subtype of [[Dart#Num | Num]]. 
2. Strings are immutable, i.e. any operation on string creates new string.
3. `double` has many methods like `round` or other methods for our use.
#### Collection/Composite Types
Composite Types are `List`(Mutable and Immutable), `Map` and `Set`. 

List are also called Array. List can also hold mixed typed elements. Arrays have looping.

Map are key-value pair. Maps are like Object in js or dictionaries in python. In Maps keys must be unique. We can add key-value also. Ironically, We can loop over Map using `forEach` or `for` which may be used for Arrays in javascript.

Sets are unordered lists of anything. It doesn't allow duplicate values. The order of elements is also not guaranteed.
```
	// LISTS
	List<int> mutableList = [1, 2, 3]; // we can use add, addAll, remove or other methods
	List<int> immutableList = const [1,2,3]; // It can't be changed.

	// MAPS
	Map<String, String> data = {  // we can use other data types also
		"user": "Bibek",
		"age": "10"
	}

	// SETS
	Set<int> numbers = {1, 2, 3};
	numbers.add(4);
	print(numbers); // {1, 2, 3, 4} as it add 4
	number.add(3) // doesn't do anything as 3 already exist in this set.

```
#### Super Type
###### [[Dart#Object | Object]]
Object is supertype of all variable in dart except null.
###### Num
It is a supertype for `int` and `double`. 
```
	num a = 10;
	a = 10.5; // Can hold both int and double
```
Tips: 
#### Special Types
###### Dynamic
Just like [[Dart#Var - Statically typed | var]] and   [[Dart#Object | Object]] , it can accept all the type. However it provides no type-safety in compile time. So, we can run any methods in it, however we might get error at run time.
```
	dynamic exg = "real";
	exg.toUpperCase() // it is ok but imagine if exg was int. it would have been accepted also.
```
Tips : 
1. It provides no compile-time checks but can accept any methods. may have run-time errors.
2. It tells dart to disable static type checking.
---
###### Object
It is a root or base-class or universal base class for all types, i.e. all the types in Dart whether `primitive` or `complex` extends from `Object`. That means it can hold any value. We can put `String` in there or `int` in there but methods available in `String` aren't available in that `Object` as they aren't same. We need to change it to `String` by type-casting to make it possible. Same for `int` or other things. Everything in Dart extend `Object` except `null`. Every variable in dart refers to Object.
```
	Object obj = "real"
	print(obj) // "real"
	obj.toUpperCase() // this is error we need to type-cast before doing this operations.

	void printData(Object data) { 
		print(data); 
	} 
	printData('Hello'); // Works with a String 
	printData(123); // Works with an int 
	printData(true); // Works with a bool
```
**Tips : 
1. We can use `Object` for value when we don't want to specify type.
2. If you have cared, it seems like  [[Dart#Var - Statically typed | var]] and `Object` seems like same but aren't same. `Var` infers meaning once variable in initialized it gives the type 
3. We can reassign value if it is declared with `Object` type but not with `var`.
###### Table View

| Feature                    | var                                                | Object                                                                 | dynamic                                                                                     |
| -------------------------- | -------------------------------------------------- | ---------------------------------------------------------------------- | ------------------------------------------------------------------------------------------- |
| **Type declaration**       | Type is inferred by Dart at runtime.               | Explicitly specifies the Object type.                                  | Explicitly specifies the dynamic type.                                                      |
| **Type flexibility**       | Fixed type after the first assignment.             | Can hold any type (base class for all types).                          | Can hold any type (no type-checking at compile time).                                       |
| **Reassignment**           | Cannot change type after initial assignment.       | Can change type with different assignments.                            | Can change type with different assignments.                                                 |
| **Compile-time safety**    | Type-safe after the first assignment.              | Has compile-time type checks for most operations.                      | No type checks at compile time (type safety is lost).                                       |
| **Method/Property access** | Limited to the methods of the inferred type.       | Limited to the methods of Object. Requires casting for specific types. | Allows access to any method or property (may cause runtime errors if accessed incorrectly). |
| **Example**                | `var name = 'John';`                               | `Object name = 'John';`                                                | `dynamic name = 'John';`                                                                    |
| **Usage scenario**         | Use when type can be inferred and will not change. | Use when you want flexibility but still want some type safety.         | Use when you need maximum flexibility and don't mind losing type safety.                    |
| **Best practice**          | Preferred when type can be inferred.               | Preferred for flexible but type-safe assignments.                      | Should be avoided unless absolutely necessary.                                              |
| **Performance**            | Fast due to compile-time checks.                   | Slower than var (requires type-checking/casting).                      | Slower due to runtime checks and lack of optimizations.                                     |
| **Cast required?**         | No, casting is not needed.                         | Yes, casting may be needed to access specific methods or properties.   | No, but could cause runtime errors if incorrectly used.                                     |
#### Asynchronous Type
###### Future
It denotes the value or error that will be available in future.
```
Future<List<dynamic>> fetchData(String url) async {
  final response = await http.get(Uri.parse(url));

  if (response.statusCode == 200) {
    return jsonDecode(response.body); // This returns a List<dynamic>
  } else {
    throw Exception('Failed to load data');
  }
}
```
Lesson Learned : 

###### Stream 

## Keywords
###### final
It is when we want to assign something at runtime. Like we don't know the value and we want to assign when program runs.
```dart
final String varName = "real";
```
###### const
It is used to declare to assign compile time constant. The value is fixed during compilation, i.e. along development. It is normally used for initialization for things like pi or things that will never knows.
```dart
const String varName = "real";
```
###### var
It will infer the type of variable from the initial value and it can't be changed after. however, the variable can be of any type at first. after that, it can't be changed afterwards. 
```dart
var x = 5; // the type of x is int. now wecan't change the type 
x = "real" // error

var arr = ["real", "red"]; // the type is list
var dob = 3.7; // the type is double
```
###### [[#Dynamic]]
Ironically dynamic is keyword also.
###### late
It initialize the variable at first without any type. But It is guaranteed that it will be of non-null.
```
late String name;
```
## Control Flow
### Looping
It can be done with `For Loop`, `do while` and `while`
### Branching
Branching can be done with `if`, `if else if`, `if else`. We can also use `break` and `continue`. No need to know syntax when AI is available.
## Functions
Since Dart is object-oriented language, every function is object. so it has type. The type of `Function`. If function doesn't have any returnType, it has `void` type. 
```
	bool isFunction(int number) {
	  return something;
	}
```
## Classes
Classes are the templates to create Object, a factory for creating Object. When we create a object, it gets all the methods that are presented in class. 
###### Classes and Object.
```dart
class Person {
	String name;
	int age: 

	// Constructors
	Person(required this.name,required this.age); // Here required isn't necessary for positional     arguments or nullabe (?) as ? indicates the variable can be null.

	// We can also have Default parameters like this
	Person(this.name = "",this.age = 10)

	void getDetails() {
		print("$name :  $age");
	}
}

void main() {
	// Here, p is the 

	Person p = Person("John", 30) // positional arguments
	Person p = Person(name: "John", age: 30); // named parameters
	Person p = Person(); // Default parameters


	p.getDetails(); // It prints name and detail of that instance.
}
```
###### Encapsulation/Privacy
The core concept of encapsulation is building the properties (data) and behavior (methods) into one unit called object. It also restrict the access of properties from outside and only allows to do so through methods.
```
class User {
	final string name;

	User(this.name)

	// setter meaning it set the value
	void setName(String name) {
		name = name;
	}

	// getter meaning we the value through it for the instance
	void getName() {
		return name;
	}
}

void main() {
	User user = User("Ram");
	user.setName("Bibek"); // it updates the name for this instance
}
```
###### Inheritance 
It allows for one class to inherit the properties and methods of another class or parent class. It also enable code reuse and establish relation among classes. There are two terms.
1. Child class or subclass
2. Parent class or Super class

> [!NOTE] What if child and parent have same methods or variable
> Then, child will override the parent automatically. we can use `super` keyword to call parent method or variable
###### Polymorphism
It refers to the ability of different objects to respond to the same method in their own way. It's the idea that you can use the same method name, but the actual behavior will depend on the object calling the method. Polymorphism allows different classes to be treated as if they are instances of the same class, providing different behaviors for the same method. There are two types of Polymorphism.
1. Method overriding - ==run time polymorphism==
The main example in dart is `@override`. overriding means redefining the implementation of methods in sub class(present class) that already exist in parent class . In Dart, what we know is the build method already exist in `Statefull or Stateless` widgets and with override we redefine the meaning of that method. It means that we are intentionally overriding the method and will raise error if the method in not present in parent class. 

> [!important] What does it means ? It can be confusing
> Polymorphism allows us to write `Vehicle vehicle = Car()` and `Vehicle vehicle = Bike()` and treats them same. They both may have `start()`. However they may start differently. We aren't concerned with how they start.

```dart
class Animal {
	void sound() {
		print("Animal Sound");
	}
}

class Dog extends Animal {
	@override
	void sound() {
		print("Dog Sound");
	}
}

class Cat extends Animal {
	@override
	void sound() {
		print("Cat Sound");
	}
}

void main() {
	Animal animal = Animal();
	Animal cat = Cat();
	Animal dog = Dog();

	animal.sound(); // animal Sound
	cat.sound(); // cat Sound
	dog.sound(); // dog Sound
}
```
2. Method overloading - ==compile time polymorphism==
It is the type of where we have multiple methods of same name in same class occurs. It is solved by looking at parameters. But, `dart` doesn't support it inherently.
###### Abstraction
The idea is we don't need to know how it works. rather we can just use it and get result. At first we provide that this method will exist. Then subclasses are then responsible for providing their own implementation of these methods.
```dart
abstract class Vehicle() {
	void start();
	void stop(); // here we are initializing the methodss but child classes will write implementation.
}

class Car extends Vehicle {   
	@override  
	void start() {     
		print("Car is starting");   
	}    
	
	@override   
	void stop() {     
		print("Car is stopping");   
	} 
}  

class Bike extends Vehicle {
	@override 
	void start() {
		print("Bike is starting");
	}

	@override 
	void stop() {
		print("Bike is stopping");
	}
}

void main() {
	Vehicle myCar = Car(); 
	Vehicle myBike = Bike(); 
	
	myCar.start(); // Car is starting 
	myCar.stop(); // Car is stopping 
	
	myBike.start(); // Bike is starting 
	myBike.stop(); // Bike is stopping
}
```
## Null Modifiers