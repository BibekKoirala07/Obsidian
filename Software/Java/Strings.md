is final, implement CharSequence, space are character, isEmpty vs isBlank
immutable. as they are used everywhere. 
String Pool
.intern()
concat(if equal could be true) but there is nuance
String Builder -> mutable
StringBuilder → preferred for normal single-threaded construction

StringBuffer → synchronized/thread-safe legacy-ish choice

start inclusive, end exclustive all over java

# Java `String` — The Complete Mental Model

If you truly understand these things, you should be comfortable reading almost any normal Java code involving strings:

1. What `String` actually is
2. Why `String` is immutable
3. String literals
4. The String Pool
5. `==` vs `.equals()`
6. `new String(...)`
7. `intern()`
8. Compile-time constants
9. String concatenation and `+`
10. `StringBuilder` vs `StringBuffer`
11. `String` performance
12. `null` vs empty string
13. Unicode, UTF-16, `char`, code points
14. Important String methods
15. `substring()` behavior
16. `equalsIgnoreCase`, `compareTo`, `contentEquals`
17. `String` and `CharSequence`
18. `StringJoiner`, `StringTokenizer`, `split`
19. Regex-related traps
20. Modern Java String features
21. Security considerations
22. Common interview traps

Let's build it from the inside out.

---

# 1. First: `String` is an Object

This:

```
String name = "Bibek";
```

does **not** mean Java has a primitive string type.

`String` is a class:

```
java.lang.String
```

So:

```
String name;
```

means:

> `name` is a reference variable capable of referring to a `String` object.

Unlike:

```
int age = 25;
```

where `int` is primitive.

This matters because:

```
String a = "hello";
String b = "hello";
```

involves references and objects.

---

# 2. The Most Important Property: String Is Immutable

This is probably the single most important String concept.

Consider:

```
String s = "hello";

s.toUpperCase();

System.out.println(s);
```

Output:

```
hello
```

Why?

Because:

```
s.toUpperCase()
```

doesn't modify the existing `"hello"` object.

It creates/returns another String.

You need:

```
s = s.toUpperCase();
```

Now:

```
HELLO
```

The key mental model:

```
"hello"
   ↑
   s
```

After:

```
s = s.toUpperCase();
```

you have conceptually:

```
"hello"        "HELLO"
   ↑              ↑
 old object       s
```

The original String wasn't modified.

---

# 3. Why Is String Immutable?

This is an extremely common interview question.

There isn't just one reason.

## Reason 1 — Security

Strings are used everywhere for sensitive values:

```
String username;
String password;
String filePath;
String className;
String databaseUrl;
```

Suppose Java allowed arbitrary modification of String objects.

That would make security-sensitive operations much harder to reason about.

For example:

```
FileInputStream("important.txt");
```

The path being represented by a String shouldn't suddenly change underneath the operation.

---

# 4. String Pool Depends Heavily on Immutability

Consider:

```
String a = "hello";
String b = "hello";
```

Java can reuse the same String object.

Conceptually:

```
        ┌─────────────┐
a ─────►│   "hello"   │
        └─────────────┘
             ▲
             │
b ───────────┘
```

If Strings were mutable, this would be dangerous.

Imagine:

```
a.changeTo("goodbye");
```

Then `b` could unexpectedly become `"goodbye"` too.

Immutability makes sharing safe.

---

# 5. String Pool

This is another **must-understand** concept.

When you write:

```
String a = "hello";
String b = "hello";
```

Java doesn't necessarily create two objects.

String literals are stored in a special pool maintained by the JVM.

So:

```
String a = "hello";
String b = "hello";

System.out.println(a == b);
```

prints:

```
true
```

Why?

Because both references point to the same pooled String object.

---

# 6. But `==` Does NOT Compare String Contents

This is one of the biggest Java interview traps.

`==` compares references when used with objects.

```
String a = new String("hello");
String b = new String("hello");

System.out.println(a == b);
```

Output:

```
false
```

Because:

```
a ───► "hello" object #1

b ───► "hello" object #2
```

The contents are equal, but the objects are different.

Use:

```
a.equals(b)
```

Output:

```
true
```

### Remember:

```
==
```

means:

> Are these references pointing to the same object?

While:

```
.equals()
```

means:

> Do these objects consider their contents equal?

For Strings, `.equals()` compares character content.

---

# 7. The Famous Example

Memorize the behavior, but more importantly understand **why**:

```
String a = "hello";
String b = "hello";

String c = new String("hello");

System.out.println(a == b);
System.out.println(a == c);
System.out.println(a.equals(c));
```

Result:

```
true
false
true
```

Because:

```
String Pool

       "hello"
        ▲   ▲
        │   │
        a   b


Heap object

       "hello"
         ▲
         │
         c
```

---

# 8. What Exactly Does `new String("hello")` Do?

This:

```
String s = new String("hello");
```

is almost always unnecessary.

You're explicitly asking Java to construct a new String object.

Conceptually:

```
Pool:
"hello"

Heap:
new String object containing "hello"
```

So:

```
String a = "hello";
String b = new String("hello");

a == b
```

is:

```
false
```

even though:

```
a.equals(b)
```

is:

```
true
```

---

# 9. `intern()`

Now things get interesting.

```
String a = new String("hello");
String b = a.intern();
```

`intern()` asks the String pool for the canonical pooled representation.

So:

```
String a = new String("hello");
String b = a.intern();

System.out.println(b == "hello");
```

prints:

```
true
```

Conceptually:

```
                 String Pool
                    │
                 "hello"
                    ▲
                    │
                    b

Heap:
new String("hello")
        ▲
        │
        a
```

---

# 10. Why Does the String Pool Exist?

Primarily to allow reuse of immutable String objects.

Imagine an application processing millions of repeated strings:

```
GET
GET
GET
GET
GET
...
```

Creating a separate object for every identical literal would be wasteful.

Because Strings cannot change, sharing them is safe.

---

# 11. String Literals vs Runtime Strings

This distinction is extremely important.

```
String a = "hello";
```

`"hello"` is a literal known by the compiler.

But:

```
String x = getInput();
```

is determined at runtime.

And:

```
String a = new String("hello");
```

explicitly creates another object.

---

# 12. Compile-Time Concatenation

Look at:

```
String a = "hello" + "world";
```

The compiler knows both values.

It can effectively turn this into:

```
String a = "helloworld";
```

So:

```
String a = "hello" + "world";
String b = "helloworld";

System.out.println(a == b);
```

can be:

```
true
```

because the concatenation is a compile-time constant expression.

---

# 13. Runtime Concatenation

Now:

```
String x = "hello";
String a = x + "world";
String b = "helloworld";

System.out.println(a == b);
```

Don't reason about this as simply:

```
"hello" + "world"
```

The value of `x` is treated as runtime data.

Therefore you shouldn't expect `a == b` to be true.

For content:

```
a.equals(b)
```

is what matters.

---

# 14. `final` Makes This More Interesting

Consider:

```
final String x = "hello";

String a = x + "world";
String b = "helloworld";

System.out.println(a == b);
```

Because `x` is a compile-time constant variable, the compiler can fold the expression.

So this can become:

```
String a = "helloworld";
```

This is a classic interview question.

---

# 15. String Concatenation

You've probably written:

```
String name = "Bibek";
String message = "Hello " + name;
```

Java supports `+` for String concatenation.

But don't think that Strings themselves are being mutated.

Conceptually:

```
"Hello " + name
```

produces another String.

---

# 16. What Happens With `+` Internally?

Historically, Java compiled string concatenation into `StringBuilder` operations.

Conceptually:

```
"Hello " + name + "!"
```

was roughly like:

```
new StringBuilder()
    .append("Hello ")
    .append(name)
    .append("!")
    .toString();
```

Modern Java has more sophisticated compilation/runtime mechanisms for concatenation, but the important conceptual point remains:

> Repeated concatenation isn't equivalent to mutating a String.

---

# 17. This Is Bad

```
String result = "";

for (int i = 0; i < 10000; i++) {
    result += i;
}
```

Why?

Every concatenation creates another String result.

Conceptually:

```
""
"0"
"01"
"012"
"0123"
...
```

That's a lot of intermediate objects/work.

Instead:

```
StringBuilder builder = new StringBuilder();

for (int i = 0; i < 10000; i++) {
    builder.append(i);
}

String result = builder.toString();
```

---

# 18. `StringBuilder`

`StringBuilder` is mutable.

```
StringBuilder sb = new StringBuilder();

sb.append("Hello");
sb.append(" ");
sb.append("Bibek");

System.out.println(sb);
```

It maintains a mutable character sequence.

This is fundamentally different from String.

```
String:
immutable

StringBuilder:
mutable
```

---

# 19. `StringBuffer`

You also have:

```
StringBuffer
```

It is similar to `StringBuilder`, but its methods are synchronized.

Historically it was designed for thread-safe use.

Generally:

```
StringBuilder → preferred for normal single-threaded construction

StringBuffer → synchronized/thread-safe legacy-ish choice
```

Don't automatically reach for `StringBuffer` just because threads exist; choose based on actual sharing/concurrency requirements.

---

# 20. String Is `final`

This:

```
public final class String
```

means you cannot subclass it.

You cannot do:

```
class MyString extends String {
}
```

Why?

Immutability/security/design guarantees are part of the reason.

---

# 21. String's Internal Representation

This gets interesting for interviews.

Historically, Java Strings were represented using a `char[]`.

Conceptually:

```
String
 └── char[]
      ├── h
      ├── e
      ├── l
      ├── l
      └── o
```

Modern JDK implementations use a compact-string representation internally, allowing Latin-1-compatible strings to use one byte per character where possible, and UTF-16 otherwise.

You generally **shouldn't write code depending on the internal representation**.

The API abstraction is what matters.

---

# 22. `char` Is Not "A Unicode Character"

This is an important distinction.

Java:

```
char
```

is a 16-bit UTF-16 code unit.

It does **not** necessarily represent a complete Unicode character.

For example, many common characters fit in one `char`.

But some Unicode characters require **two UTF-16 code units**.

That's where:

```
codePointAt()
```

and related APIs matter.

---

# 23. `length()` Can Surprise You

For normal ASCII:

```
String s = "hello";

System.out.println(s.length());
```

returns:

```
5
```

But:

```
String s = "😀";

System.out.println(s.length());
```

doesn't necessarily return `1`.

It returns the number of UTF-16 code units.

For supplementary Unicode characters, that can be `2`.

So:

```
String length
≠
necessarily number of human-perceived characters
```

This is an excellent interview distinction.

---

# 24. Code Points

If you need Unicode code points:

```
String s = "😀";

s.codePointCount(0, s.length());
```

This counts Unicode code points rather than UTF-16 code units.

And:

```
s.codePointAt(index)
```

retrieves a code point.

This becomes important when working seriously with international text.

---

# 25. `String` Implements `CharSequence`

String is not some isolated thing.

Conceptually:

```
CharSequence
    ↑
 String
```

`CharSequence` represents a readable sequence of characters.

Other implementations include:

```
StringBuilder
StringBuffer
```

So APIs sometimes accept:

```
CharSequence
```

rather than:

```
String
```

because they don't need specifically a String.

---

# 26. Important String Methods

You need to know these extremely well.

---

## `length()`

```
String s = "hello";

s.length();
```

Returns UTF-16 code-unit count.

---

## `isEmpty()`

```
"".isEmpty();     // true
"hello".isEmpty(); // false
```

Important:

```
"   ".isEmpty()
```

is:

```
false
```

because spaces are characters.

---

# 27. `isBlank()`

Modern Java:

```
"".isBlank();      // true
"   ".isBlank();   // true
"hello".isBlank(); // false
```

This considers whitespace.

So:

```
isEmpty()
```

and:

```
isBlank()
```

are different.

---

# 28. `charAt()`

```
String s = "hello";

char c = s.charAt(1);
```

Result:

```
'e'
```

Indexes are zero-based.

```
h e l l o
0 1 2 3 4
```

Invalid index:

```
s.charAt(10);
```

throws:

```
StringIndexOutOfBoundsException
```

---

# 29. `substring()`

```
String s = "hello";

String x = s.substring(1, 4);
```

Result:

```
"ell"
```

Remember:

```
start inclusive
end exclusive
```

This convention appears all over Java.

```
h e l l o
0 1 2 3 4
  └─────┘
  1 to 4
```

---

# 30. Modern `substring()` Memory Behavior

Older Java implementations had interesting behavior where a substring could share the original backing character array.

That created situations where a tiny substring could keep a huge original String's character storage alive.

Modern implementations copy the relevant characters into a new representation instead.

This is one of those Java evolution facts worth knowing for interviews.

---

# 31. `equals()`

```
String a = "hello";
String b = new String("hello");

a.equals(b);
```

returns:

```
true
```

It compares String contents.

---

# 32. Null Safety

This is dangerous:

```
String name = null;

name.equals("Bibek");
```

You get:

```
NullPointerException
```

A common safe pattern:

```
"Bibek".equals(name)
```

Now if:

```
name == null
```

the result is simply:

```
false
```

because the literal is non-null.

---

# 33. `equalsIgnoreCase()`

```
"hello".equalsIgnoreCase("HELLO");
```

returns:

```
true
```

But don't casually assume case-insensitive comparison is equivalent to full Unicode/culture-aware text comparison. International text can be considerably more complicated.

---

# 34. `compareTo()`

```
"apple".compareTo("banana");
```

returns a negative number.

Conceptually:

```
apple < banana
```

And:

```
"banana".compareTo("apple")
```

returns positive.

Equal strings:

```
"hello".compareTo("hello")
```

returns:

```
0
```

The important contract is:

```
negative → less than
0        → equal according to ordering
positive → greater than
```

Don't rely on the exact numeric value.

---

# 35. `compareToIgnoreCase()`

Same idea, ignoring case differences.

---

# 36. `contains()`

```
"Hello World".contains("World");
```

returns:

```
true
```

---

# 37. `startsWith()` / `endsWith()`

```
"hello.java".startsWith("hello");
"hello.java".endsWith(".java");
```

Very common.

---

# 38. `indexOf()`

```
String s = "hello";

s.indexOf('l');
```

returns:

```
2
```

because the first `l` occurs at index `2`.

If it isn't found:

```
s.indexOf('x');
```

returns:

```
-1
```

This `-1` convention is worth remembering.

---

# 39. `lastIndexOf()`

```
"hello".lastIndexOf('l');
```

returns:

```
3
```

---

# 40. `contains()` vs `indexOf()`

You can conceptually think:

```
s.contains("abc")
```

as asking:

> Does this sequence exist?

while:

```
s.indexOf("abc")
```

asks:

> Where does it start?

---

# 41. `replace()`

```
String s = "hello";

String x = s.replace('l', 'x');
```

Result:

```
hexxo
```

Remember: original remains unchanged.

---

# 42. `replaceAll()` Is Different

This is an interview trap.

```
replace()
```

performs literal replacement.

But:

```
replaceAll()
```

uses **regular expressions**.

Example:

```
"abc123".replaceAll("\\d", "");
```

Result:

```
abc
```

Because:

```
\d
```

means a digit in regex.

---

# 43. `replaceFirst()`

Also regex-based.

```
"123123".replaceFirst("\\d", "X");
```

Result:

```
X23123
```

Only the first matching regex occurrence.

---

# 44. `split()` Uses Regex

Another huge interview/practical trap.

```
String s = "a,b,c";

String[] parts = s.split(",");
```

Fine.

But:

```
s.split(".");
```

does **not** mean "split on literal dot."

`.` has special meaning in regex:

> any character

So use:

```
s.split("\\.");
```

for literal dot.

This distinction causes tons of real-world bugs.

---

# 45. `trim()` vs `strip()`

Older:

```
trim()
```

removes characters according to older ASCII-oriented rules.

Modern Java:

```
strip()
stripLeading()
stripTrailing()
```

are Unicode-aware whitespace operations.

So for modern text handling, understand the distinction rather than blindly using `trim()`.

---

# 46. `toLowerCase()` / `toUpperCase()`

```
String s = "HELLO";

s.toLowerCase();
```

returns:

```
hello
```

Again, Strings remain immutable.

But here's an important internationalization issue.

Case conversion can depend on locale.

So there are overloads involving:

```
Locale
```

When you're writing locale-sensitive software, don't assume English casing rules apply universally.

---

# 47. `concat()`

```
"Hello ".concat("Bibek");
```

produces:

```
Hello Bibek
```

But most Java code uses:

```
"Hello " + "Bibek"
```

because it is more natural.

---

# 48. `valueOf()`

This is extremely common.

```
String.valueOf(123)
```

returns:

```
"123"
```

There are overloads for many primitive types and objects.

Interesting case:

```
String.valueOf((Object) null)
```

returns:

```
"null"
```

rather than throwing an NPE.

That's different from:

```
someObject.toString()
```

which would throw if `someObject == null`.

---

# 49. `toString()`

Every object ultimately inherits `toString()` from `Object`, and String itself is obviously a String.

For your own classes, overriding `toString()` is extremely useful:

```
@Override
public String toString() {
    return "Person{name='" + name + "'}";
}
```

---

# 50. String Conversion and `+`

This:

```
String s = "Age: " + 25;
```

automatically converts the integer into String representation.

Similarly:

```
String s = "User: " + user;
```

involves string conversion of the object.

If `user` isn't null, this ultimately involves its `toString()` representation.

---

# 51. The `null` Trap With `+`

Consider:

```
String s = null;

System.out.println(s + "hello");
```

This does not necessarily throw NPE.

You can get:

```
nullhello
```

because string concatenation converts the null reference into the textual representation `"null"`.

That's different from:

```
s.length();
```

which throws NPE.

---

# 52. `formatted()` / `format()`

Modern Java gives you formatting APIs.

For example:

```
String message = "Hello %s, age %d".formatted("Bibek", 25);
```

You can also use:

```
String.format(...)
```

Useful, but understand that formatting is more expensive than simple concatenation when you don't actually need formatting machinery.

---

# 53. Text Blocks

Modern Java also has text blocks:

```
String json = """
        {
            "name": "Bibek",
            "age": 25
        }
        """;
```

Very useful for:

- SQL
- JSON
- HTML
- multiline text
- configuration snippets

Instead of:

```
String x = "{\n" +
           "  \"name\": \"Bibek\"\n" +
           "}";
```

---

# 54. `String.lines()`

Modern Java:

```
String text = """
        hello
        world
        java
        """;

text.lines()
```

returns a stream of lines.

Very convenient for text processing.

---

# 55. `repeat()`

Modern Java:

```
"ha".repeat(3);
```

produces:

```
hahaha
```

---

# 56. `indent()`

Modern String APIs also include methods useful for multiline text formatting, such as:

```
indent()
```

Useful when dealing with generated or structured text.

---

# 57. `transform()`

A more functional-style API:

```
String result =
    "hello"
        .transform(String::toUpperCase);
```

Conceptually:

```
String
  ↓
function
  ↓
new value
```

Not something you need every day, but you'll encounter it in modern Java code.

---

# 58. `String.join()`

Extremely useful.

```
String result = String.join(", ", "Java", "Python", "JavaScript");
```

Result:

```
Java, Python, JavaScript
```

You can also use an `Iterable`.

---

# 59. `StringJoiner`

Useful when constructing joined text incrementally:

```
StringJoiner joiner = new StringJoiner(", ");

joiner.add("Java");
joiner.add("Python");
joiner.add("JavaScript");

System.out.println(joiner);
```

Result:

```
Java, Python, JavaScript
```

You can also define:

```
prefix
delimiter
suffix
```

---

# 60. String vs StringBuilder

This distinction should become automatic in your head.

### String

```
String s = "hello";
```

Immutable.

### StringBuilder

```
StringBuilder sb = new StringBuilder();
```

Mutable.

Use String when you're representing actual textual values.

Use StringBuilder when you're **constructing** a String through repeated modifications.

---

# 61. A Very Important Performance Rule

This:

```
String s = "";

for (...) {
    s += something;
}
```

can become expensive because of repeated String creation/copying.

Prefer:

```
StringBuilder sb = new StringBuilder();

for (...) {
    sb.append(something);
}

String s = sb.toString();
```

But don't turn this into dogma.

This:

```
String fullName = firstName + " " + lastName;
```

is perfectly fine.

Don't write:

```
new StringBuilder()
    .append(firstName)
    .append(" ")
    .append(lastName)
    .toString();
```

for every tiny concatenation.

Modern Java compilers/JVMs optimize straightforward concatenation well.

---

# 62. StringBuilder's `toString()`

At the end:

```
String result = builder.toString();
```

you get a String representation.

That String is immutable.

So the common workflow is:

```
StringBuilder
     ↓
mutate repeatedly
     ↓
toString()
     ↓
immutable String
```

---

# 63. Capacity of StringBuilder

A `StringBuilder` maintains an internal buffer with a capacity.

You can create one with an initial capacity:

```
StringBuilder sb = new StringBuilder(100);
```

This can reduce buffer resizing if you know approximately how much data you're going to build.

You can inspect:

```
sb.capacity();
sb.length();
```

Important difference:

```
length   = characters currently stored

capacity = available internal buffer capacity before growth
```

---

# 64. `StringBuffer` vs `StringBuilder`

Interview version:

||String|StringBuilder|StringBuffer|
|---|---|---|---|
|Mutable|❌|✅|✅|
|Immutable|✅|❌|❌|
|Thread-safe operations|immutable|no synchronization|synchronized methods|
|Main purpose|represent text|build text|synchronized mutable text|

---

# 65. String and Hashing

This matters enormously with collections.

Strings override:

```
equals()
hashCode()
```

consistently.

Therefore:

```
Map<String, Integer>
Set<String>
```

work properly based on String content.

Example:

```
Set<String> names = new HashSet<>();

names.add(new String("Bibek"));
names.add(new String("Bibek"));

System.out.println(names.size());
```

Result:

```
1
```

Why?

The two String objects are different references, but:

```
equals()
```

says they have equal contents, and their hash codes are equal.

---

# 66. Why String Immutability Helps HashMap

Imagine:

```
Map<String, Integer> map = new HashMap<>();

String key = "hello";

map.put(key, 100);
```

The String's hash code won't change because its contents cannot change.

That's very important.

If mutable keys could change their hash-relevant state after insertion, hash-based collections could become problematic.

---

# 67. String Hash Code

For Strings:

```
"hello".hashCode()
```

is calculated from its characters according to String's defined hash algorithm.

Two equal Strings must have:

```
a.equals(b) == true
```

and therefore:

```
a.hashCode() == b.hashCode()
```

But the reverse isn't guaranteed:

```
a.hashCode() == b.hashCode()
```

does **not** prove:

```
a.equals(b)
```

because hash collisions can exist.

---

# 68. String as a `Map` Key

Very common:

```
Map<String, User> users = new HashMap<>();

users.put("bibek", user);
```

Then:

```
users.get("bibek");
```

works based on String equality/hash behavior.

---

# 69. `String` and Switch

Modern Java supports switching on Strings:

```
switch (command) {
    case "start" -> ...
    case "stop" -> ...
    case "restart" -> ...
}
```

Historically Java implemented String switch using mechanisms involving hashing and equality checks rather than directly switching on String object references.

That's another nice interview implementation fact.

---

# 70. String Pool Is Not the Same Thing as "All Strings Live There"

This misconception is important.

People often say:

> "Strings are stored in the String Pool."

Too simplistic.

Not every String object you create magically becomes the pooled object.

For example:

```
String a = new String("hello");
```

creates a distinct String object.

The literal `"hello"` belongs to the pool.

You can explicitly use:

```
a.intern()
```

to obtain the pooled canonical representation.

So think:

```
String objects exist
        +
String pool is a mechanism for canonical pooled strings
```

---

# 71. String Pool Location

Historically, the String pool had a different relationship with JVM memory areas.

Modern JVMs maintain interned Strings in the heap.

This is another example where old Java interview explanations can be outdated.

If someone tells you:

> "String pool is in PermGen."

That's an old-Java explanation.

PermGen was removed in Java 8.

Modern JVMs use the heap for the interned String pool.

---

# 72. String Interning and Memory

You shouldn't blindly call:

```
intern()
```

on enormous amounts of arbitrary user-generated data.

Interning creates/maintains canonical representations and has memory/runtime implications.

It's useful when you have a genuine reason to canonicalize repeated values, not something you sprinkle everywhere.

---

# 73. `String` Is Thread-Safe — In an Important Sense

People sometimes ask:

> Is String thread-safe?

Because String is immutable, its state cannot be modified after construction.

That makes sharing String instances between threads inherently safe with respect to mutation.

Compare:

```
String
immutable → safe to share

StringBuilder
mutable → concurrent access requires care
```

This doesn't mean every operation involving Strings is automatically thread-safe. Your surrounding mutable state can still be unsafe.

---

# 74. String and Encoding

This is where many programmers get confused.

There are three different ideas:

```
characters
   ↓
Unicode code points
   ↓
UTF-16 representation in Java String

bytes
   ↓
UTF-8 / UTF-16 / ISO-8859-1 / etc.
```

A Java String represents text.

A byte array represents bytes.

When converting between them, you need an encoding.

---

# 75. String → bytes

Don't casually do:

```
text.getBytes();
```

because it uses the platform default charset.

Prefer explicit encoding when the protocol/file format requires one:

```
text.getBytes(StandardCharsets.UTF_8);
```

---

# 76. bytes → String

Similarly:

```
new String(bytes, StandardCharsets.UTF_8);
```

rather than relying on platform defaults.

This matters enormously in production systems.

Especially when moving between:

```
Linux
Windows
databases
HTTP
files
Kafka
APIs
```

---

# 77. UTF-8 vs UTF-16

Don't confuse:

```
String's internal representation
```

with:

```
external encoding
```

A Java String isn't "UTF-8."

UTF-8 is an encoding used to convert Unicode text to/from bytes.

Example:

```
byte[] bytes = text.getBytes(StandardCharsets.UTF_8);
```

Now you have UTF-8 encoded bytes.

---

# 78. `String` and Regex

Methods such as:

```
matches()
replaceAll()
replaceFirst()
split()
```

involve regular-expression semantics.

This creates a very common double-escaping problem.

For example:

Regex:

```
\d
```

Java String representing that regex:

```
"\\d"
```

Why?

Because Java itself interprets `\` as an escape character.

So there are two layers:

```
Java String escaping
        ↓
regex escaping
```

This is a very important practical skill.

---

# 79. `matches()` Trap

```
"123".matches("\\d+")
```

returns true.

But understand:

```
matches()
```

checks whether the **entire string** matches the regex.

It's not simply:

> Does this regex occur somewhere inside the string?

That's a common misunderstanding.

---

# 80. `contains()` vs `matches()`

For:

```
"hello123"
```

this:

```
contains("123")
```

asks:

> Does `"123"` occur somewhere?

while:

```
matches("\\d+")
```

asks:

> Is the entire string one or more digits?

So:

```
"hello123".matches("\\d+")
```

is:

```
false
```

---

# 81. String Deduplication

Modern JVMs can have GC-related mechanisms that reduce duplicate String backing data under certain configurations.

This is an implementation optimization, not something your program should rely upon semantically.

The language-level concept you should understand is still:

```
String immutability
+
interned literals
```

---

# 82. `String` vs `char[]`

Sometimes you need mutable character data.

Then:

```
char[]
```

may be appropriate.

Historically people also used `char[]` for sensitive data like passwords because it can be overwritten:

```
char[] password = ...
```

whereas String is immutable and cannot be manually cleared.

However, modern security-sensitive code often uses dedicated credential/secret handling APIs rather than assuming `char[]` alone solves secret exposure.

---

# 83. Empty String vs `null`

These are completely different:

```
String a = "";
String b = null;
```

`a` refers to a real String with zero characters.

`b` refers to no object.

Therefore:

```
a.length(); // 0
b.length(); // NullPointerException
```

Mental model:

```
"" → object exists, length = 0

null → no String object
```

---

# 84. Whitespace String

Also:

```
String s = "   ";
```

is neither:

```
empty
```

nor:

```
null
```

It contains whitespace characters.

Modern Java:

```
s.isBlank()
```

is useful here.

---

# 85. `String` Equality and Case

Don't do:

```
if (name == "Bibek")
```

for content comparison.

Do:

```
if ("Bibek".equals(name))
```

or:

```
if (name != null && name.equals("Bibek"))
```

depending on your null semantics.

---

# 86. Why `String` Is So Special in Java

You can technically think:

```
String
```

is just another class.

But Java gives it special language-level treatment.

For example:

```
String s = "hello";
```

has literal syntax.

And:

```
"hello" + "world"
```

has language-supported concatenation.

And modern Java supports:

```
switch (s)
```

Text blocks are also built into the language.

So String sits at the boundary between:

```
ordinary Java class
+
special language support
```

That's a useful conceptual point.

---

# 87. A Very Important Interview Question

### Why is String immutable and final?

A strong answer should mention several dimensions:

> String is immutable so its contents cannot change after creation. This enables safe sharing, makes String suitable as a hash-based collection key, supports String pooling/interning, and provides useful security and concurrency properties. String being final prevents subclasses from changing its behavior in ways that could undermine these guarantees.

That's much stronger than:

> "Because security."

---

# 88. Another Interview Question

### Difference between:

```
String s1 = "hello";
String s2 = "hello";
```

and:

```
String s1 = new String("hello");
String s2 = new String("hello");
```

First:

```
same pooled literal
```

Second:

```
two explicitly created String objects
```

Therefore:

```
s1 == s2
```

can be:

```
true
```

for the first case and:

```
false
```

for the second.

But:

```
s1.equals(s2)
```

is true in both.

---

# 89. Another Classic Interview Question

What does this print?

```
String a = "Java";
String b = "Ja" + "va";

System.out.println(a == b);
```

Answer:

```
true
```

because the concatenation consists entirely of compile-time constants.

Now:

```
String x = "Ja";

String b = x + "va";

System.out.println(a == b);
```

Don't expect the same result because the concatenation is no longer purely a compile-time constant expression.

---

# 90. Another One

```
String a = "hello";
String b = new String("hello");

System.out.println(a == b);
System.out.println(a.equals(b));
```

Answer:

```
false
true
```

If you can explain that without memorization, you've got the concept.

---

# 91. Another One

```
String a = "hello";
String b = new String("hello").intern();

System.out.println(a == b);
```

Answer:

```
true
```

Because `intern()` gives you the canonical pooled String.

---

# 92. Another One

```
String s = "hello";

s.concat(" world");

System.out.println(s);
```

Output:

```
hello
```

Because:

```
concat()
```

doesn't mutate `s`.

Correct:

```
s = s.concat(" world");
```

---

# 93. Another One

```
String s = "hello";

s.replace("h", "H");

System.out.println(s);
```

Still:

```
hello
```

Again:

> String methods generally return a new String rather than modifying the existing one.

---

# 94. The Mental Model You Should Have

Whenever you see:

```
String s = ...
```

think:

```
s
│
└──── reference
          │
          ▼
      immutable String
```

Whenever you see:

```
s = someStringMethod(...)
```

think:

```
old String remains unchanged

s ─────► new String
```

Whenever you see:

```
"literal"
```

think:

```
String pool
```

Whenever you see:

```
new String(...)
```

think:

```
explicitly creating a separate String object
```

Whenever you see:

```
a == b
```

think:

```
same object?
```

Whenever you see:

```
a.equals(b)
```

think:

```
same String content?
```

---

# 95. The Bigger Java Picture

String connects to many Java concepts you've already been studying.

```
                    String
                      │
       ┌──────────────┼───────────────┐
       │              │               │
   Object          CharSequence    Comparable
       │              │               │
   equals()       text APIs       compareTo()
   hashCode()                     ordering
   toString()
       │
       ▼
Collections
HashMap<String, ?>
HashSet<String>
```

And:

```
String
  │
  ├── immutable
  │
  ├── String Pool
  │
  ├── equals/hashCode
  │
  ├── Unicode
  │
  ├── encoding
  │
  ├── regex
  │
  ├── StringBuilder
  │
  └── performance
```

That's why Strings show up in so many interview questions.

---

# 96. What You Actually Need to Master

For a serious Java interview, I'd divide String knowledge into four levels.

### Level 1 — Must be automatic

You should instantly know:

```
String is immutable
String is final
String pool
== vs equals()
new String()
intern()
null vs ""
length()
charAt()
substring()
indexOf()
replace()
split()
```

### Level 2 — Strong interview knowledge

Understand:

```
compile-time constant strings
String concatenation
StringBuilder
StringBuffer
hashCode()
String as HashMap key
CharSequence
UTF-16
code points
String ↔ byte[] encoding
```

### Level 3 — Advanced

Know:

```
compact strings
String pool implementation evolution
substring implementation evolution
regex interaction
locale-sensitive casing
Unicode complications
JVM optimizations
string concatenation invokedynamic/runtime behavior
```

### Level 4 — Real backend engineering

Be comfortable with:

```
UTF-8
HTTP text
JSON
database encoding
file encoding
Unicode normalization
regex
large text processing
StringBuilder
memory allocation
logging
security-sensitive strings
```

---

# 97. The 10 Things I'd Drill Into Your Brain

If you're revising Java for interviews, remember these:

**1. String is an object, not a primitive.**

**2. String is immutable.**

**3. String literals are interned in the String pool.**

**4. `==` compares references; `.equals()` compares String contents.**

**5. `new String("x")` explicitly creates another String object.**

**6. `intern()` gives you the canonical pooled representation.**

**7. `StringBuilder` is mutable and is useful for repeated construction.**

**8. `split()`, `replaceAll()`, `replaceFirst()`, and `matches()` involve regex semantics.**

**9. Java `char` is a UTF-16 code unit, not necessarily a complete Unicode character.**

**10. A Java String is text; when communicating with bytes, you need an explicit charset such as UTF-8.**

If those ten are solid, you're already past the "I know how to call `substring()`" level.

---

## One final challenge

Before moving on, I would actually test your understanding rather than give you more theory.

Try to predict the output of this **without running it**:

```
public class Main {
    public static void main(String[] args) {

        String a = "Java";
        String b = "Java";
        String c = new String("Java");
        String d = c.intern();

        final String x = "Ja";
        String e = x + "va";

        String y = "Ja";
        String f = y + "va";

        System.out.println(a == b);
        System.out.println(a == c);
        System.out.println(a.equals(c));
        System.out.println(a == d);
        System.out.println(a == e);
        System.out.println(a == f);

        String s = "hello";
        s.toUpperCase();

        System.out.println(s);

        StringBuilder builder = new StringBuilder("hello");
        builder.append(" world");

        System.out.println(builder);
    }
}
```

If you can explain **why every line prints what it prints**, including `e` vs `f`, then you've genuinely understood the core of Java Strings rather than memorized String APIs.