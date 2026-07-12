OOP is the way to model real-world objects as software objects which contain both data and code. OOP is class based programming.

If the fields is static, then there is only one copy in the memory and this value is associated with the class or template itself. If not static, it is called instance fields.

Any methods that operates on instance fields needs to be non-static.

Classes can be organized into logical grouping which are called packages. If you don't declare a package, the class implicitly belongs to the default packages.

Classes is said to be a top-level class if it is defined in the source code file and not enclosed in the code block of another class, type or method. For them, we have only two valid access modifier options: public or none(default)

One thing is confusing. Protected access modifier allows classes in the same packages also not only children.

Encapsulation in OOP usually has two meanings. One is bundling of behavior and attributes on a single object. Other is the practice of hiding fields and some methods from public access.

null is a special keyword in java, meaning the variable or attribute has a type but no reference to an object. This means that no instance or object is assigned to the variable or field. Fields with primitive data types are never null. But remember strings aren't primitive types. All the primitives data types are automatically assigned by java. Boolean is false, int and its types in 0, Double is 0.0.

###### NullException