	* Purely functional Programming language means that forbid changing variable values. They has their own use. JS isn't it.

	** Non-Primitve Data-Types
	- Object 
	
	- typeof // it is actually an operator 
	  typeof(null) // it gives object. This is official recognized error.
	  typeof(Math) // Math serves as a built in objects
  	  typfof(alert) // gives function as it is function

Basic Operators -- operands --> All operator returns a value
	** On the basis of number of operands
	- Unary Operator eg y = -x //having only one operand. Here, - is unary operator
	- Binary Operator eg. t = y + x // having two operand
	** Math
	- Math Operator - (+, -, * , /, %, **: exponential)
	** String Concatenation with binary +
	- '1' + 2 --> "12", 4 + 4 + '8' --> "88", "1" + 4 + 3 --> "143" 
	** Numeric Concatenation with unary +  // goal: to convert String to number, basically like Numberic Conversion
	- +true --> 1, +false --> 0 // basically it is just like Number(true)
	- let one = '1', two = '2' and  one + two --> 12 but +one + +two --> 3
	- we use unary + cuz it has higher precedence than binary +
	** Operator Precedence
	- unary+ 14 > unary- 13 >  ** 12  >  *  >  /  >  +  >  -
	** Assignment Operator
	- Assigment operator ( = ) has value of 2 and it also returns a value like any other operators eg x=2*2+1. so all operator 
	  perform their operations and assigment operator return the value to x
		eg let x = 1, y = 2
		   let c = 3 - (x = y + 1) // here the latest value of x is 3 and c = 0. First the result of expression x = y + 1 is 
		      stored in x and value of x is updated and further calculation is done
	** Modify and Place -- +=, -=, *=, /=, ++, --, a++ --> return old value of a but a is increased, ++a --> return new value of 	   a but a is increased
	** Bitwise Operator
	- &, |, XOR(^), NOT(~)
	** Comma Operator -- precedence value 1
	- , is used such as a = (1+2, 3+4) // the value is 5. it is used such as for(a = 1, b=3, c=a*b)

Comparision-Operators
	- == is done with type Conversion
	- === is odne without type Conversion
	** Conditional Branching
		- If elseif operator &&  Conditional(Tertiary) Operator '?' eg. let isGreater = (age > 20) ? "yes" : "no", Multiple 	  	  Conditional Operator

Logical-Operators
	- ||, console.log( ramesh || null || value ) --> getting first truthy values or gets last value if all are falsy.
	  it stops or immediately goes to another line as soon as we encounter first truthy value and ignore whatever lies after.
	  like for true || alert("damn") or false || alert() --> this is extremely useful
	    --> every value is converted to boolean before calculation
	- &&, like OR , get first falsy value or gets last value if all are truthy. eg. ram || "bibek" || null --> null
	- !(NOT), let a = !value. it convert value to boolean and gives opposite of that. it's precendence higher than other LO	 

LOOPING 
	** While Loop, Do While Loop
	** For Loop syntax: for(begin; condtion; step) // any part can be skipped, break and continue can be used. Suppose there are 	  two for loop, then break and continue only skip inner one. If we want to break both then we should use label
	  eg. outer: for() inner: for() { if() break outer;} 
	

Function
	
	** Arrow functions
	- Arrow functions are another form of function expression

	













Values
	-- Number // Js has allocated 64 bits for number so we can have 2^64 values i.e. an 18 with 18 zeroes behind it. but in real it
		is 18 with 15 zeroes as we have to denote negative and whole numbers
	-- Strings // "", '', ``, \n indicates new line
	-- Boolean

OPERATORS
	--
