![[Pasted image 20250408192021.png]]

### Statements
It is the complete commands to be executed. It can include one or more expression.
java is "Write once, work everywhere". 
There are different types of statement. One is Declaration Statement. It is used to define a variable by indicating the data type and the name.
```java
	int name = 5;
```

Expression is coding construct, that evaluates to a single value. or a code segment that is on the right side of the equals sign in an assignment or declaration statement.

```java
	int threeExpress = (20 + 12) + (12 * 7);
	// Here (20 + 12) is one expression and (12 * 7) is another expression and        whole thing is another expression.
	int anotherExpresion = 12 + 23 + 45; // here each number are expression.
```

Variable are stored in RAM.
Primitive Data types are simply placeholders in memory for a value.
How to know the range of `int`.
```java 
	int myMinValue = Integer.MIN_VALUE;
	int maxValue = Integer.MAX_VALUE;
```

We need two things. both java(run the application) and javac(compile the code)

### Wrapper Class
If we add 1 more than `Integer_MAX_VALUE`, it creates overflow and for other it creates underflow. They are also called Integer wraparound. Integer wraparound event, either overflow or underflow, can occur in java when you are using expression that aren't simple literal value.
So, java don't do anything for those situation if it involves expression but we can't directly declare number bigger than `MAX_VALUE` and less than `MIN_VALUE`. We can actually write
```java
	int num = INTEGER.MAX_VALUE + 1; // no error as it is epxresion
```
![[Pasted image 20250408203505.png]]
### Byte, short, long and their width
![[Pasted image 20250408203610.png]]
![[Pasted image 20250408204352.png]]
Size if available in `Datatype.SIZE`. The main thing to know is that whole by default is assigned as integer in java
```java
	long longNumber = 100; //it may seems to be long but it isn't. that 100 is int and not a long as by default any whole number is integer in java
	long longNumber = 100L; // it is long

	byte newByteValue = (myMinByteValue/ 2); // this will cause error. as that expresion is calculated at run time                                                  and not at compile time and that is regarded as int and not byte.
```
Use int unless you are really good reason not to.
### Decimal numbers
`Double` is default. The precision and width is different. Precision refers to the format and amount of space occupied by the relevant type.
![[Pasted image 20250408211036.png]]

For float, use suffix of lowercase `F`. float aren't generally recommended to use these days. java libraries like math functions are used to process doubles and not float. double numbers are faster than equivalent float.
In general, if we need extreme precision like in payment app or any other apps, `float` and `decimal` aren't used. Java has`BigDecimal` are used.

### Char boolean and String
Char using just one string would normally means that it may occupy just one byte of memory, but no it occupy two bytes of memory. Thus has width of 16. The reason is that char is stored as 2 byte number, similar to `short`. This number gets mapped to a single character by java. So when you print a char, you will see the mapped character, and not the representative number.  Java supports a unicode value for characters, which you can use to set a char value.

String is technically only limited by the amount of memory space or Heap space in your computer which turns out to be max underscore value of an INT which turns out to be around `2.14` billion. So that is a lot of character that could fit in the string.
### Arithmetic using data types

### Class
Class is the way of creating own data types. Classes by itself are custom data types. ![[Pasted image 20250408223318.png]]
Code block

### Mastering Java Expression, Statements, Code Blocks and Methods
Java has 51 keywords.

==Everything in java is object except main method==. We do it to avoid deadlock. From somewhere program have to start right ? Then from where ? to call main we would need object to that class. but that isn't viable at all.

We also have jshell(java shell)
Type jshell in command prompt and see it.

### How java works ?
Java have JVM(Java Virtual Machine). It makes java code platform independent. We need JVM to make java code work. If you don't have JVM, we can't run java. The work of JVM is to execute Java code. However JVM will not accept code directly. We can't directly write a code that run on JVM. Java says, if you want to run java code, it will run on JVM. Java is platform independent but JVM itself is platform independent. JVM itself can't run on `ios`. So we need to check if JVM is available for that `OS`. JVM takes bytecode. We have JVM and below that we have OS and below that we have hardware.

==Java is platform Independent however JVM itself is platform dependent==.
![[Pasted image 20250309112133.png]]
If we install JDK, we will get JVM and JRE
![[Pasted image 20250309112347.png]]
==WORA==. Write once, run anywhere.

By default the number with decimal is double . It needs to be converted for it to become float.
Primitive - Simple value stored directly in memory(stack)
Non-primitive - Memory address(stack) that points to the heap

### Literals


### Type conversion
Byte has from `-127 - 127`. what if we cast int to byte. it gives remainder. 

### Type Promotion
![[Pasted image 20250309115450.png]]


### Operators
###### Arithmetic

###### Relational
###### Logical 

### OOP
objects are created by JVM. manufacturing of your object is done by JVM. What we are concerned is with designing the object.
![[Pasted image 20250310181931.png]]
new methods just consume the space of Calculator class object.

All of the java code is executed by JVM. But most of the times, when we want to compile the bytecode. it is dependent on external files like inbuilt classes and inbuilt libraries. So we need something extra than JVM. that extra thing is JRE. it has that. It has all the extra classes, files and it also validate your bytecode also. Like laoding a class. But ultimately your code will run on jvm.

![[Pasted image 20250321233543.png]]
that object creation is one step and object assignment is another step. so we are doing two step at once.. 
![[Pasted image 20250321233631.png]]


### Stack and Heap
Inside JVM, we execute the code. Let's focus on memory level. Stack is our traditional stack and heap is the memory which can expand. That stack would have key and value.
![[Pasted image 20250321221818.png]]

```java
class Cal {
	public Cal() {
		
	}

}

public static void main() {
	Cal cal = new Cal(); //that cal isn't object. It is referencing variable. that is it.
	int r1 = cal.add(2, 3);

}
```
![[Pasted image 20250321222633.png]]

Every method will have its own stack. Local are part of stack and Instance variable are part of heap.

Whenever we create some object/reference variable, it is done in Heap and the address will be pointed to that object in stack. that object have two things right ? instance variable and methods. Instance variable are stored in heap and for methods, new stack are made. However they are mentioned in that heap also. it points towards that.

Why is object actually a reference variable. Cuz object is in heap and not in stack.so


### Array
If we know initial value and not know initial value
###### jagged and 3D array
jagged means no size is mentioned
###### Drawbacks
Collection
### Strings
```java
    String name = "Bibek"; // string class is so used that java makers made this way of writing. nowadays nobody write STring name = new String(""); Behind scene, it is exactly happening that. but it is little bit absraction to make our life easier.
```
It actually is a class.

Immutable and mutable strings(  stringbuffer and stringbuilder ). By default, strings are immutable.
They are created in Heap and they are constant. Every string I create is new thing in heap.

```
StringBuffer sb = neww StringBuffer();
```
It will give us string size which is of 16 bytes.
![[Pasted image 20250321225440.png]]
Stringbuffer takes 16 bytes extra for relocation. if it stores 5 bytes then the total space taken will be 16 + 5.

StringBuffer is threadSafe and StringBuilder isn't

### Static
all object share that thing. We can't use instance variable there. to initialize static variable use static block. It is called before constructor. Static variable is done when Class are loaded. It just happens once.
![[Pasted image 20250310202534.png]]

![[Pasted image 20250310202133.png]]
There is different area in JVM where static variables are stored. learn about static block. It is called only once no matter how many times you created a object. It run only one time in the time of creating Class only. 

Every time you create a object. two things happens. Class Loads and Objects are instantiated. The thing is class will load only one in entire program. In JVM, it has special place called Class Loader. so class will get loaded from your library to class Loader. And it will happen only once. Everytime you load a class, it will call static block.

Have you ever tried not creating object. ? it will also not load the class but remember we can use load function in Class Class in java something like `Class.forName()`. try that without creating object. you can see it will run that static block. Tada.

==In case of confusion, the reference is always given to local variable==.
### Constructor 
Always assign the value in constructor. default and parameterized constructor.
### Naming conventions
camelCasing. Class and interface should be capital. for variable and methods, camelCase. constant should be full cpaital

### Anoymous Object
![[Pasted image 20250321233731.png]]
It can be used only once. so use it with caution.

### Inheritance
Single and Multilevel Inhertiance. and Multiple Inheritance(Not possible in C# and Java, is possible in C++).
![[Pasted image 20250310211541.png]]
### this and super
![[Pasted image 20250310212233.png]]
![[Pasted image 20250310212311.png]]
Every constructor has a method name called super(). It is there even if we don't mention. it calls the parent 

==Every single class(which doesn't inherit other class) in java extends Object class==. Like in above figure A, not B. Cuz B extends A. B extends A, A extends Object.
### Method Overriding
Some times I might get confused. that what if B extends A and have same method. What if we make B object in main class ? if we call that method in that B object. which method will be called ? Don't get confused. the method in B will be called. It has been source of confusion for me.

### Packages
`*` means importing all files. not folder. 
When we are sharing the package to the world, the name of packages should be unique.

### Access Modifier
By default, the variable is only visible to packages.
![[Pasted image 20250310215146.png]]
don't use default. for class public. for instance private
### Polymorphism
![[Pasted image 20250310225916.png]]
### Dynamic Method Dispatch
I need to see this in details.
```java
	Computer laptop = new Laptop() // whatever will be in laptop will be called of Laptop object.
```
### final keyword
can be used variable, method(can't rewrite in children/ can't override ) and class(can't be inherited).
### Object equals to String hash code
Every class extends Object class in java(don't get confused as int, bool aren't object, they are primitive types). so java isn't 100 % OOP language. But it isn't downside. as creating object is expensive task and rather than using heap which is relatively slow compared to stack, java stores primitives in stack. it makes execution faster. They are used very much so.
Every time you try to print the object it calls `toString` object which return string. It gererates hash on the basis of all the data that we have. like we have 3 variable in class. It generates single hash that contains those variable.
### Upcasting and Down casting
see more about this
![[Pasted image 20250322120508.png]]


We don't need to mention it. It happens behind the scenes.
### Wrapper Class
There are certain features that only works with Object but primitives aren't Object. There are certain features that only works with object. Like Collection only works with Object. But Array can have primitive values. 
==For Every primitive type, there is Object for that==.
![[Pasted image 20250310232834.png]]
boxing, autoboxing, unboxing and autounboxing. the main theme is that 
```java
	int numOne = 9; // is an primitive
	Integer num = new Integer(numOne) // is being converted to Object, this is called boxing This phenomenon is so common that java provides easier way to do this
	Integer num = numOne; // in background it is exactly above one happening.

	int numTwo = num.getValue(); // this is unboxing and we can do it direct. it is called autounboxing
	
```

## ==See that project==
### Abstract
Abstract methods should belongs to Abstract class However abstract class can have normal methods. we can't create a object of abstract class but we can instantiate it.

==If we fails to instantiate that method in child class. it gives error. to remove that error we should make that child class as abstract. But we can't instantiate child class ta da.==.

### Inner Class
![[Pasted image 20250322123908.png]]

![[Pasted image 20250311224033.png]]
### Anonymous Inner class 
If we are using that class just one time, we can use anonymous inner class. look at the picture below. the only purpose of B is to override show method. If it is the only purpose. Why do that ?
![[Pasted image 20250322124149.png]]
![[Pasted image 20250311224331.png]]

![[Pasted image 20250311224518.png]]

### Abstract Anonymous Inner class
We can combine abstract an anonymous inner class. We may need that if child class of abstract class has just one implementation. So why create that child class for just that purpose.
### Interface
What if all the methods of abstract class is `public abstract` ? means implementation will be provided later ? We can make it interface. All the methods in interface are `public abstract`. We use interface to design something. all the variable in interface are final and static. That means we need to define that variable as it is final. 
![[Pasted image 20250311225416.png]]
interface can also extend other interface.

If we fails to implement all the methods in child class, it has to be made `abstract class`.

Interface don't have their own memory in heap.

###### Need of interface


###### Types of Interace
After java 8, we got option of defining the method in interface, previously we could only declare the method in interface.
If interface has two or more method, it is called Normal Interface. The interface with no methods in called marker interface. We create this to talk with compiler. ==Know more about Marker Interface==.
![[Pasted image 20250315133542.png]]
![[Pasted image 20250315133742.png]]
functional interface is cool as we can only use lambda expressions with functions only.
![[Pasted image 20250315134507.png]]
lambda expression can also have return value. It just simplifies anonymous inner class. Lambda expression increase the size of file but not by that much



### Enums
![[Pasted image 20250322130909.png]]

They are named constant. Enum itself is class. we can define variable, methods, constructor but generally Named constant are made. but not extendable. ==Enums aren't extendable ==apart from this. all the things are same. We can implement getter and Setter also.
Switch works good with enums.
![[Pasted image 20250315131015.png]]
We can use `orginal` method of enum class to use that `0 1 2 3`.

### Enums Class
![[Pasted image 20250315132012.png]]
### Annotation
It just means supplement or some information to compiler or runtime. It shows the intention of programmer. They starts with `@` like `@Overide`.  There are many annotation. they may works on variable, methods or class level.

### Exception
Exception is parent class to other classes like Arithmetic Exception or other class.
Compiler will force us to handle Checked Exception but Unchecked Exception should be handle by us for overall better experience.
###### Exception Hierarchy
![[Pasted image 20250315152734.png]]

### Ducking exception using throws
We can duck(pass exception to parents). Like A is parent for B. then if exception occurs at B. then if we use some keyword in B, it can send the exception to A. So, The circle can go on and on. Don't do that in main. It tells JVM to handle that rather than handling by out code

### Taking input from user
We need to have `BufferRead`. from `java.io` package.
Buffer Reader can take input from anywhere like file, Network. not just system keyboard.
But nowadays we use Scanner class rather than buffer Reader.
use try with resources . we need to use finally or resources to close the db connection or other things if that fails in db connection.

### Threads
Smallest unit that you can work with. we can run multiple threads at same time.
The beauty of task is that it can run parallelly. They are light weight.
In every thread class
```
public class A extends Threads
```
we need to have run methods.
we can run those threads in main class with 
```
	objA.start();
	ojbB.start();
```

Nowadays we have multiple core CPU. meaning that if we have 8 core CPU, then we can execute 8 threads at same time. We have high amount of threads(can check in Task Manager). So what does CPU does ? It uses concept of time sharing with the help of scheduler.

==Normally one thread is performed for one millisecond ==

We can't control the scheduler. What we can do is suggest the scheduler to give the priority to certain thread.

We can set priority for threads and Sleep also.

The things is we can make thread by extending thread class or use runnable class also.
### Race Conditions
threads and mutation isn't good idea. If you are working with threads try to work with data which is immutable. or understand the concept of thread safe. it means only one thread can work with that at one point. Let's say we have `show()` method. If thread one is executing that method, then thread two is unable to execute that methods when thread one is being executed.

Let's say we have two thread and a `Counter` class with increment method and count variable. If we make two thread in main function and implement that in both thread you can see some irregularities. Like then thread one(t1) and thread two(t2) is created, there total are three thread. that extra one is permannent one (main thread). if we don't tell the program to wait till t1 and t2 don't finish computation and print counter variable anyway , we can see that the count variable is less. We need to wait. But evern that isn't enough. What if thread one and thread two run same function in same time ? It causes race condition. It produce huge irregularity.

We can avoid that using synchronize function. 

### Thread states
![[Pasted image 20250322154724.png]]

### Collections
Collection API - Concept
Collection - Interface
Collections - Class
![[Pasted image 20250315164203.png]]


![[Pasted image 20250315163441.png]]

### Comparable vs Comparator
Comparator is the interface using which we can specify our own logic for sorting.
![[Pasted image 20250322165134.png]]

### Needs of Stream API

Stream can be used only once.


### Parallel Stream

### Mavens
It is a project management tools. Helps with version management. Lets say we . When we build a 

project we need to do this ![[Pasted image 20250312114229.png]]normally we can do the upper described things with IDEs. 

We may need external libraries. In java, libraries normally means `jar` files. Know about transitive dependency.

but sometimes we need extra things like external libraries(connecting to database).
We can download maven from apache maven. We can get it from itellij also. Different IDE have their own build system.
If you are building web project using eclipse, all your web content goes to web content folder. However in intellij, we might have different name. So if we do project in eclipse and share it with someone who uses itellij, and they try to run that. it causes problem. So if our build system is maven, it solves that. For maven we can lifecycle options like `compile`, `packages`, `run` and many more. To achieve that, maven provide its own plugins.
###### Archetype
It is a templating tools. Let's say we want to build a web project, we can do setup and add the configuration or we can use the existing template. We can also create it. Let's say in company we build multiple project with same type. so we can create a template. So for this type of project you will get all the configs and dependencies mentioned, so that whoever want to build the new project, they can use that. Maven also can handle the dependencies.

In the world of maven, the dependencies are found in jar file. we can go to `mvnrepository` and 
Maven Archetype
### JDBC
It is an API. It is a part of Java Development Kit. The problem is we have multiple Database. As DBMS changes, the code to connect to that Database also changes. What if in future, we want to connect to `MySql` ? How many lines of code that you are going to change ? What java says is we have JDBC API and we will give you interfaces and few classes but the actual implementation will be given by DBMS itself. So if postgres says, I have DBMS and allows java application to connect, they have to provide implementation for that. Same for MySQL. So if want to connect Java application to DBMS, from java side, it uses JDBC API but you need actual implementation from DBMS. They must provide some library to work with.
![[Pasted image 20250312130739.png]]
To achieve this we need `jar` file.

The DBMS should provide a library(jar file) which is a implementation of JDBC connector. then we have to write java code. JDBC follows different steps. Different books says different number of steps. some say 5, 6, or 7.

1. Once you got your jar file. Import the package (java.sql)
2. Load the driver. The driver is coming from jar file. Every implementation have different driver.
3. Register the driver. 
4. Create the connection
5. Create statement
6. Execute Statement
7. Close the connection
Some say 6 steps as number 1 is sometimes regarded as optional. Some say Load and Register as single step. In previous version of JDBC. loading and registering was compulsory after that it wasn't. so there is ambiguity but mainly it is 7 steps.

### Hibernate- and ORM framework
It is a tool which helps to save data to database. Spring MVC is based in Servlets.
We use Hibernate, instead of plain JDBC.

### Spring Getting Started
IOC
the user is doing this
![[Pasted image 20250323214730.png]]
As a programmer you are doing this. When you try to do this, your main focus doesn't go to business logic. What you want is you want someone else to control this thing. It means inverting the control. Earlier we had control. Now we are giving to someone else. That is not just from Object creation but for Control flow also. To achieve this we have IoC container. In Spring you don't need to create the object, Spring will.
![[Pasted image 20250323215202.png]]
But how exactly will spring achieve it ? There we will use Dependency injection. You have object there but someone has to inject the object in application.

Dependency injection(Design Patterns) is actual implementation of IOC(principle).

###### Autowiring


there are three way of injection
1. Constructor injection
![[Pasted image 20250308190107.png]]

2. Setter injection
![[Pasted image 20250308190048.png]]

3. Loose coupling ![[Pasted image 20250308191548.png]]
### Exploring Spring Framework
Any objects managed by spring is called `beans`.
Spring will have its own container(IOC). We need to create a container. Every time we ask spring to create a object for you, it will create the object and keep that in container.

We need to use `ApplicationContext`. we have older one which is `BeanContext`. Both are nearly same. Both help to build the container and get the object from the container.

But when we type `Applictioncontext`, there is no autocomplete features. Why cuz it isn't part of java. So, we need to get some packages from internet. We need to add that spring dependencies
Let's get that from maven repository. So what are the classes that implements `ApplicationContext` ? One of the classes is `ClassPathXmlApplicationContext` through the means of `spring.xml`.
```java
	ApplicationContext context = new ClassPathXmlApplicationContext(); // this creates the container for you
```
Infact there are many way of configuring spring project. One of the way is XML one. Then we will move to java base and to annotation base.
```java 
	ApplicationContext context = new ClassPathXmlApplicationContext(); // this creates the container for you
Alien obj  = (Alien) context.getBean("alien");
```
`getBean` will give us object from the container. But by default. it will give us type of Object as `Object` and we need to cast it.

If this wasn't done through spring, we would do by this 
```java
	Alien alien = new Alien();  
	alien.code();
```
when we use context(`ApplicationContext`), it means that spring is handling that object creation and object giving things.
```java
	Alien obj  = (Alien) context.getBean("alien");
```
Here we are asking the spring framework with the help of application context to create the object.

But this will give you error. But spring say it can't find the `Alien` class. But why ? We need to be talking to spring framework by telling that it is your responsibility to manage the `Alien` class. There are different way to talk to spring
![[Pasted image 20250322215513.png]]
Since we are using `ClassPathXmlApplicationContext`, it will look for XML file in Class Path which is main folder. There create the `resources` folder and create a xml file. can give it any name. For demo create `spring.xml`.
There create a bean tags. bean are basically objects.
![[Pasted image 20250322220517.png]]

```java
<bean 
id="alien"(we write this in getBeans but get Alien) class="com.example.Alien"
>

</bean>
```
bean are basically object managed by spring . Mention name of bean and class. Like we mention class referencing name in `id` and class also. which class alien but we should provide full path.

We need to use those tags that spring understand. Of course , XML can understand whatever it can.
don't worry we can find this xml in internet. here , I am just trying to understand. we need to search in internet and copy it. Whatever class need to be added or to be used or managed by spring, we should tell it to spring in xml. like 
```java
<bean id="alien" class="com.example.Alien">

</bean>
``` 
and do this in `ApplicationContext`.
```java
	ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml"); // this creates the container for you
Alien obj  = (Alien) context.getBean("alien");
```
This get alien class from any package through the medium of `spring.xml`.

TLDR: That application Context put every class that is mentioned in `spring.xml`. Whatever class are required. and we can use `getBean` to get that class from that container whenever it is required.

Question to ask.
So when exactly is spring creating the object in spring container ? does it happen in `ApplicationContext` line or `getBeans` line. If you comment out that `getBeans` line you can put the constructor in that class. you can see that that constructor do run. 

So , in `ApplicationContext` line. It is telling to create the container and go to `spirng.xml` file and create the object and put it in container.

Question to ask
```java
<beans>
	<bean id="user1" class="com.example.User">
	
	</bean>
	<bean id="user2" class="com.example.User">
	
	</bean>
</beans>
```
How many object are created ? Don't get confused. Both are created despite the class are same.  

Another Question
Let's say `Alien` class is written in beans. How many object are created for this example. 
```java
	Alien obj1  = (Alien) context.getBean("alien");
	obj1.code();

	Alien obj2  = (Alien) context.getBean("alien");
	obj1.code();
```
Well they are referencing the same object in spring framework.
you can check by introducing variable in `Alien` class.
```java
Class Alien {
	public int age;
	public Alient{
		this.age = 21;
	}
} // this is in alient class to check if they are different 
```
Let's check
```java
    Alien obj1  = (Alien) context.getBean("alien");
	System.out.println(obj1.age); // 21

	Alien obj2  = (Alien) context.getBean("alien");
	System.out.println(obj2.age); // 21 
```
So why same ? cuz `getBeans` refers to same object no matter how many times it is called 

When we talk about any beans. it has different scopes. They are `Singleton`, `Prototype`, `Request`, `Session` or another. But in spring code we just use two. `Singleton` and `Prototype`. That `Request` or `Session` works for web or websockets. By default, spring follows `Singleton`. that means in your application, Only one object is created for that bean and no matter how many time it is called that reference is passed
what if we want to change that ? We need to change the scope in bean.
```java
	<bean id="user1" class="com.example.User"               scope="singleton" >
	
	</bean>
	<bean id="user1" class="com.example.User"               scope="prototype" >
	
	</bean>
```
For singleton, it is initialized in `ApplicationContext`. and it is reused every time `getBeans` class it. But for `prototype`. it isn't initialized when container is created. but created like normal. every time it is called, new object are created.

==Spring believes in injection You should remember that==
let revisit that Alien class which has age variable and code method. It was initialized in `ApplicationContext` in `spring.xml`. right ? 
So what if we want to set up property but not from code ? in `spring.xml` ?
```java
	<bean id="user1" class="com.example.User">
		<property name="age" value="21"></property> // for primitive it is value. but for non primitive like other object
		// we can provide value to multiple attributes through diferent property
		// since it uses setter, it is called setter injection
		// we can say we injected the property
	</bean>
```
You can see but getting the value of age to be `21` from `getBeans`. but We can ask one question. How exactly is it providing the value ? is it from setters or through variable ? If we do it, we can see it is calling from setter object. So the things to know is that bean first make the object, search for setter and then only it will execute the other parts. if there is no setter, it gives error.

Another Question
The property we injected is normal/primitive variable. the question is what if property isn't normal variable. What if it is a reference ? 
We can do this by this way. Let say we have this in Alien
```java
public class Alien {
	private int age;
	private User user;
}
```
```java
	<bean id="alien" class="com.example.Alien">
		<property name="age" value="21"></property>
		<property name="user" ref="user1"></property>
	</bean>
	
	<bean id="user1" class="com.example.User">
	
	</bean>
```
So TLDR, for primitive value we use `value`, for reference object we use `ref`. And even if we don't write `setters`, it is created by `ApplicationContext` and the value is injected.

But in OOP, we normally assign value in constructor right ? setters are good but they are used later to assign.
So how to inject constructor ? 
```java
	<bean id="alien" class="com.example.Alien">
		<constructor-args value="21" />
		<constructor-args ref="user1" />
		// <property name="age" value="21"></property>
		// <property name="user" ref="user1"></property>
	</bean>
```
It works.
If we change the order of constructor, it fails.
like this. it fails
```java
	<bean id="alien" class="com.example.Alien">
		<constructor-args ref="user1" />
		<constructor-args value="21" />
	</bean>
```
Why cuz in constructor we had
```java
	public Alien(int age, User user)
```
So order matters.

So how to combat this ? You can use type and index. You can use internet to search that. We can use @ConstructorProperties also.

Let's talk about ==Autowiring==
![[Pasted image 20250323001857.png]]
Let's say we want spring to handle the wiring process ? 
let say we have Alien class like this 
```java
	Public class Alien {
		private int age;
		private Computer com;
	}
```
We can see that com is interface. So Whatever the name is mentioned in Alien class is used as it. Like if the bean explicitly mentions or not ? If not mentioned, then it goes to id name if it mentions then it goes to itself. You can look at it later.


### Primary bean
when we use auto wiring by type, it can create confusion. We use primary bean to solve that.
![[Pasted image 20250323180949.png]]
In case of confusion, that primary one will be selected.

### Lazy bean init
![[Pasted image 20250323181257.png]]
It is just like lazy loading in react. We know that `ApplicationContext` create all the object in `spring.xml`. What if we don't want to do that ? Remember Singleton pattern creates all the object in `ApplicationContext` and prototype only does that when we use `getBeans`. For singleton, what if we don't want to create the class in container and only when we use `getBeans`? Once created, when ever it is required, normally singleton is used. just like lazy loading in react, we just use that thing whenever it is needed. it is done by `lazy-init.`

### Get beans by type
==Every interface gets compiled to .class file==

### Inner beans

### Progressing With java based Config
remember, we use xml based approach. right ? We can also use java based config. In this way, how we communicate with container changes. `AppConfig`(name does not matter) will replace the `spring.xml`.

For both xml and java based, you are using same container, the only thing that changes is how we communicates with container

It xml we were using `bean` tag, we use Bean annotation.

In xml, we had bean name in `id` but the name of bean in java based in name of the function or we can use it in like this. Here desktop is also bean name and 'com2', "desktop1" and "Beast" are also bean name.
![[Pasted image 20250323191540.png]]
![[Pasted image 20250323191625.png]]

By default every bean will be singleton. Just like in bean what if I want to use prototype ? and not singleton. ? 
We can use `@Scope` annotation. 
like 
```java
      @Scope("prototype")
```

###### Component Stereotype Annotation
`@Component` is extremely necessary.

###### Autowired
`@Autowired` and `Qualifier` is extremely necessary. There are three kinds of injection. They re field injection, Constructor injection and setter injection. In is preferrable to use setter injection.
```java
	@Autowired  
	public void setComputer(Computer computer) {  
		System.out.println("here...");  
	    this.computer = computer;  
	}
```

###### Primary Annotation
Either you need to use Qualifier or Primary annotation. If we use both just like in xml. Qualifier will take preference over Primary.

###### Scope and Value Annotation
`@Scope("prototype")` We can inject the value just like in xml by @Value annotation.
```java
	@Value("21")  
	private int age;
```

We are progressing
Optionally, That `id` field is optional. Then What will `getBeans` object call to refer the required class ? 

### Moving to Spring Boot

### Spring JDBC
Why not use Normal JDBC ? Remember the step that we need to manage. there were like 5 or 7 steps. We can automate that using Spring JDBC.

### Spring Boot Web
What is Web app ? Spring as a framework, it has multiple project inside it. It is umbrella term for multiple projects. And there are multiple projects that deals with web application. There is Spring Web or if you don't want to use Spring Boot then we can use Spring MVC there.  We build server in java with servlets. We can't simply run the servlets on JVM. since it has some extra features like accepting the request from internet and responding, we have to use special container. That is called Servlets container or Web container. One of the option in Tomcat, it is a server in which we can run our servlets. The goal of spring is to make sure you are using servlets in easy format behind the scene. that is it. Behind spring, servlets is being used. it is just a abstraction. Another option to servlets in Reactive also. even if you are using Spring MVC or Spring Web, it is using servlets behind the scene.

We need servlet container which is Tomcat server. What you do is you write your code(web application) in java and you create a package of it. and normally we create a package with `.war` which is web archive. If you are building console application, we can use `.jar` but for web application we use `.war` as it runs on Tomcat. But nowadays we can also create `.jar` which you can directly run(I will mention is later) But the most important thing is that you have to create a package of your project and put that on Tomcat server so that you can run it. That means we need tomcat server in the machine.

the main thing is we have to put our project in package and put it in tomcat server so that that server can run it.

You can go to google and search for Apache tomcat and download it. We can see things like this.
![[Pasted image 20250324163932.png]]
We put our project in webapps folder and to start we go to bin folder and there is `startup.sh`. But what if we want to tomcat in built ? It is called embedded tomcat. The advantage is that you don't need to do that config. But if you really want to build in servlet, then rather download tomcat and use that external tomcat as it gives more features.

To create servlet, go to maven repo and type Jakarta servlet and embedded tomcat.


Introduction to MVC
JSP(Java server page) is solution to this mess. It is like templating engine. Like `EJS`, 
```java
	out.println("<p>Here is done</p>");
```
![[Pasted image 20250324173142.png]]
The object is data.
![[Pasted image 20250324173229.png]]
The thing in JSP get converted to servlet as Tomcats only process servlet.

Remember when tomcat only accept `.war` ? well in spring boot it accept can accept `.jar`. Why ? cuz spring already has embedded apache tomcat.
![[Pasted image 20250324174039.png]]
This is the reason why.

We need controller to server jsp page. JSP will be called by controller.

The job of request mapping is done by dispatcher servlet.

`Model` Object passes the data between object.

The issue is that what if different templating engine was used ? like instead of `.jsp` other thing was used. ? Should we go to every return and change the extension or what if we put those templating engines in different folder inside `webapp` ? we need to configure in `application.properties`.
Go to google and update the properties.

Put static files in `resources/static` in main folder.

ModelAndView
Model is for Data. 
###### Need for Model Attribute.
### Exploring Web MVC without Spring Boot
Didn't we talked about Spring MVC ? Basically we talked about Spring Boot MVC not Spring MVC.

Lets say use MVC just using spring.

### Rest Using Spring Boot
We can use jackson xml to send data in xml format.

### Spring JPA
JPA implement Hibernate. Even you change the tool from hibernate to something else. You don't have to change the change the lots of code because you haven't specified for hibernate in any part of the code. That common way is called JPA(java persistence API). 

Use Optional to counter Null point Exception 
![[Pasted image 20250325130957.png]]

![[Pasted image 20250325131939.png]]
But we don't need @Query at all.

### AOP
Aspect oriented Programming. It complements OOP. 
It solves the problem of cross-cutting concerns. 
Lets say apart from business logic, we may want logging facilities.
or want security, or exception handling and validation also. What to do ? Do that in controller or service ? 

AOP does that automatically. We don't' have to do that.

weaving should be done.

Understand the concept

### Spring Security
![[Pasted image 20250325160805.png]]
This is what we get when we add Spring Security. All the request will go to spring security and it verifies for all the auth and authorization. if yes, then only that request will go to dispatch servlet. filter will go one by one. Spring have a lot of filter. We can change the order of filter. However Normally we won't do that.

session Id
What is it ? When we login once in application, we can access all the resources. there will be session created for you. When you hit a particular URL, it first check the session if it is available. If session is there, there will be no need to login. We can get access to that Session Id.

Every time you login you get different session
![[Pasted image 20250325163107.png]]
We can access it by this
```java
	public String Greet(HttpSerlveltResquest req) {
		return "Hello World" + req.getSession().getId();
	}
```

remember the username was `user` and password was generated for each session ? What if we can set it ? We can go to `application.properties` and write this 
![[Pasted image 20250325163319.png]]
You know what is the problem with this ? For entire application, we have only one username and password. Don't that.

What is CSRF
Spring Security manually take care of CSRF.
let's say I have session id for my banking website and it is present in session. I go to some malicious website which takes that cookies from by browser and will take session id for banking site. It goes to that banking website and will use my session id which was taken from my browser.
It is the problem of `PUT`, `PATCH`, `POST` and `DELETE` request.
Because Get method is safe.

Every request, you get a token and you have to send it along with request also.

That token is present in `req` object also
```
	request.getAttribute("_csrf");
```

However why do so much hassle right ? What if request can be available from only one website ? remember cors from express ?

If we have to use session id then it is statefull but we can counter lots of vunerabillities just by making stateless.


###### Authentication Provider
Spring just provides you security. There are different way and types of getting Authentication. To provide that authentication, we have Authentication Provider.


### JWT and OAuth 2



### Data Structures
Way to organize data efficiently. Algorithm is set of instructions.

There are multiple data structures available

###### Abstract Data types
 Class is user defined Data type i.e. user creates class with list of primitive data types. In C, it is `struct`. Array isn't primitive data type. We are creating our own types when we use array.
 ![[Pasted image 20250313114145.png]]When we create our own type and specify what operation which we can do. then it is abstract data type. without specifying how it is implemented. Like stack. we don't know whether linked list of array can be used but we know operations like pop, push, peek.

We calculate the speed of operation with number of steps(time complexity) not by actual time taken by machine. why ? cuz in real life there are different kinds of machine with different speed, power and other things. ==Funny thing is if we compare the same algorithm in home laptop and office laptop== So it is better if we use time complexity. We also have Space Complexity(how much space/memory it takes).
![[Pasted image 20250313115638.png]]
Like let's say it takes 5 second for 10 values, then does it takes 10 second for 20 values ? 

Big O Notation/ Order of
![[Pasted image 20250313115810.png]]
Like for array
```java
	int[] arr = {3, 4, 5, 6, 7}
```
Let's say we want the `arr[2]`, we can just go in 1 step(not exactly 1 step, we don't know how many step but one iteration). So The big O Notation is Constant Time.
![[Pasted image 20250313120510.png]]
###### Linear Search
But for searching we need to go to all the element to get that item. right ? So what is the number of element there ? `n` and we must take worst case scenario. So
`O(n)`. It means it will take exactly (number of length of array) iteration. Best case is `O(1)`.
###### Binary Search
For Binary Search Time complexity is `O(log n)` base 2.
###### Sorting
Bubble Sorting
![[Pasted image 20250313122009.png]]
We can compare first element with second and sort that and do that for second and third and do that again. and repeat it. By doing it the biggest number will be at last. and again repeat that process except for last one . and again except for last two one. 
Time Complexity: `O(n^2)` as we need to iterate and compare in inner loops also.

Selection Sorting
So the things we divide the array in two section. One is in sorted section and unsorted section. 
Ok, Let's visualize.
We take the minimum value from the array and we swap the `arr[0]` with that minimum value index. and now we have lowest value at `arr[0]`. we do that to `arr[1]` and repeat that.

We can also take maximum value and put it in last. 
Time Complexity `O(n)`. Since swapping happens just at outer loop, it is better than Bubble sorting

Insertion Sorting
![[Pasted image 20250313134655.png]]
We introduce one temp variable and and we start from index 1. That index 1 number is put in temp variable. and we check if the elements in left side is bigger than temp, then that element will be shifted to right. if we are at index let's say 5, and move that number to temp, then number at index 4 is checked if the temp is larger or smaller then, if temp is smaller or index 4 is bigger than that index 4 is shifted to right. and we go to index 3 and so on. Up to which point will we go ? up to the point such that that index is smaller than temp.
![[Pasted image 20250313135923.png]]

Quick Sorting
The big this first is that it is just better as it has time complexity of `O(n)` of `nlog(n)`.
Recursion is used. Divide and Conquer is also used. Tree is also used.

Merge Sort
Divide and Conquer

###### Tree





