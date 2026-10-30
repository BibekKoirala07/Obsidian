functions are first class objects, that means it can be used just like any other object like assigning them to 
variable and passing to other functions as arguments(callback)
the function that accepts other functions as parameters are called higher order funtions and they contains logic for when
the callbacks gets executed

style. attribute
Output   .innerHTML  .document.write()   .window.alert()
Using nothing to declare variable

Hoisting


Objects {
	can also contains function inside object
}


for in for objects

JS Sets

JS Maps

Type Conversion

try ()
catch(err)

Object methods
Objects.values(object name) // returns values
Objects.keys()
Objects.freeze()
Objects.create()


Important Array Methods //Array are special type of Objects
length, isArray

toString() => coverts array to string of arr values

max and min element in array


removing and adding element from array 
pop , push, shift, unshift
splice() => add new items in desired place, can also be used to remove
splice(where to add, how many element to remove, elements to be added)
splice(2,2,"apple","mango") // doesn't return new array

make new array with existing array 
slice(from where to select, up to end argument) eg slice(1,3(optional))

flattening a array => 
flat() ==> [[1,2],[3,4]] ==> [1,2,3,4]

finding positon of element in array 
indexOf(element, starting point)
lastIndexOf()
findIndex

Array Iteration
foreach 
for of(could be used for string also)
Map  .join .split
filter
reduce .reduceRight .reduceleft
every
some
find // first value of first array element that passes a test
fill(what value to put, start index, last index) //new Array(length).fill(....)

Math methods 
round
ceil
floor
trunc
min
max
random

Importants Strings Methods
length, concat, 

if strings contains a substring |
startsWith() 
endsWith()
includes(substring,12(starting position))

finding positon of substring in string | 
indexOf(substring, where to start(optional)) //return first occurence
lastIndexOf(substring, where to start(optional)) // return index of last occurence of text

extracting a substring | //all return in new string
slice(starting,ending) //ending is optional, if we want to extract remaining characters
substring(start,end)  //same as slice but values<0 are treated as 0
substr(start,length of to be extracted part(optional)) , if not written, it will return all remaining part

changing case |  
toUpperCase() toLowerCase()

updating parts of a string | // return new string
replace(to be replaced,replacing with) ==> replace the first occurence
replaceAll() ==> replace all occurence

removing whitespace in string |
trim() // both sides of string
trimStart() // only from start
trimEnd() // only from end

Converting string to array |
split ==> split(",") //split on commas

parseInt parses its argument and return a whole numbers
parseFloat()

charAt(), charCodeAt()

Js number Methods
tostring()
toexponential
tofixed
toprecision
valueof

Events {
	onclick
	onload
	onmouseover, onmouseout
	onkeydown
	onchange
}

for UUID 
cryto.randomuuid()



toString(base 2,8,10,16,32) to change the base of the number


Comparing two JavaScript objects always returns false.

JS numbers are 64-bit and accurate upto 15 digits






