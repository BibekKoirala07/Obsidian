It is scripting language. So it is interpreted as not compiled. Every scripting language is interpreted. Like js, PHP, etc. Unlike Js in browser, PHP requires a server to run. Client makes request to server which then interprets the PHP from which it can do its thing.
![[Pasted image 20250123220958.png]]
PHP runs on webserver. Webserver can refer to hardware or software. WB in hardware just means whatever stores the webserver software, source code and some other stuff. WB in software just means it process request coming to it using different protocol(typically HTTP). It can host single or multiple website in same server using same resources.
![[Pasted image 20250123222020.png]]
There are two types of 


There are 3 ways to PHP 
### Basic PHP(Procedural)
###### Basic Php
We don't need `;` in last line as it automatically assumes that there is `;` in last line.
echo is marginally faster.

###### constant and variable
```php
	// cal at run time means we can't use it that much
	define("name", "value");
	define("STATUS_PAID", "PAID");
	echo STATUS_PAID;
	echo defined("STATUS_PAID");

	// or we can use const keyword
	// cal at compiled time
	const STATUS_PAID = "paid";
```
There are also magic constant. And We also have variable variable.
Variable in php are by default assign by value.
###### Data types
PHP is dynamically and weakly typed language.
Scalar Types - bool, int, float and string.
Compound Types - Array, Object, Callable, Iterable
Special Types - Resource, null

For String, we also have Herodoc and NowDoc. They  can have full html code also.
Null is if it not defined, 

Arrays

Expression

###### HeroDoc and NowDoc

###### include and require
require will result in error while include will result in warning. If no path is specified, then files are looked up from the directory that is said in PHP INI configuration file under the `include_path` section. If not then only it looks in current directory. 
```php
	require("file.php");
	include "file.php"
```
### Intermediate PHP(OOP)
### Advanced PHP(Security)