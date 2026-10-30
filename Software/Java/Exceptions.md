# Java Exceptions — Complete Interview Guide

## 1. What Is an Exception?

An exception is an event that disrupts the normal flow of a program's instructions. When an error occurs, Java creates an exception **object** and hands it off to the runtime system — this is called **throwing** an exception. The runtime then searches for code to handle it — this is **catching** an exception.

---

## 2. The Throwable Hierarchy

```
java.lang.Object
   └── java.lang.Throwable
         ├── Error                 (unchecked — JVM/system problems)
         │     ├── OutOfMemoryError
         │     ├── StackOverflowError
         │     └── NoClassDefFoundError
         │
         └── Exception
               ├── IOException           (checked)
               ├── SQLException          (checked)
               ├── ClassNotFoundException (checked)
               │
               └── RuntimeException      (unchecked)
                     ├── NullPointerException
                     ├── ArrayIndexOutOfBoundsException
                     ├── ArithmeticException
                     ├── ClassCastException
                     ├── IllegalArgumentException
                     ├── IllegalStateException
                     └── NumberFormatException
```

**Key interview point:** Only `Throwable` and its subclasses can be thrown/caught. `Error` and `Exception` are the two direct children.

---

## 3. Checked vs Unchecked Exceptions

||Checked|Unchecked|
|---|---|---|
|Parent|`Exception` (not `RuntimeException`)|`RuntimeException` / `Error`|
|Compiler enforces handling?|Yes — must catch or declare with `throws`|No|
|Represents|Recoverable conditions outside your control (file missing, network down)|Programming bugs (null access, bad index)|
|Examples|`IOException`, `SQLException`|`NullPointerException`, `ArithmeticException`|

**Why does this distinction exist?** Checked exceptions force the caller to think about failure modes that are external and expected (I/O, DB). Unchecked exceptions usually signal a bug that shouldn't be "handled" so much as fixed.

**Common interview trap:** "Is `Error` checked or unchecked?" → Unchecked (you're not expected to catch `OutOfMemoryError`).

---

## 4. try-catch-finally

```java
try {
    riskyMethod();
} catch (IOException e) {
    // handle
} catch (SQLException e) {
    // handle
} finally {
    // always runs (almost always — see gotchas below)
}
```

Rules:

- `catch` blocks are checked top to bottom; put **more specific exceptions first**, otherwise you get a compile error (unreachable catch block) if a subclass follows its superclass.
- `finally` runs whether or not an exception occurred, and even if the `try`/`catch` has a `return` statement.
- You can have `try-finally` with no `catch` at all.

### Multi-catch (Java 7+)

```java
catch (IOException | SQLException e) {
    log.error(e);
}
```

The exceptions in a multi-catch must not be related by inheritance (one can't be a subclass of the other), and the caught variable `e` is implicitly `final`.

---

## 5. try-with-resources (Java 7+)

Any class implementing `AutoCloseable` (which `Closeable` extends) can be used here — resources are closed automatically, in **reverse order** of declaration, even if an exception is thrown.

```java
try (FileReader fr = new FileReader("file.txt");
     BufferedReader br = new BufferedReader(fr)) {
    return br.readLine();
} catch (IOException e) {
    e.printStackTrace();
}
```

This replaced the old verbose pattern:

```java
FileReader fr = null;
try {
    fr = new FileReader("file.txt");
    // ...
} finally {
    if (fr != null) fr.close();
}
```

**Interview favorite — suppressed exceptions:** If both the `try` block _and_ the `close()` call throw, the exception from `try` is the one propagated; the `close()` exception is attached as a "suppressed" exception, retrievable via `getSuppressed()`.

---

## 6. The finally / return Gotcha (asked constantly)

```java
static int test() {
    try {
        return 1;
    } finally {
        return 2;   // this WINS — finally's return overrides try's return
    }
}
// test() returns 2
```

```java
static int test2() {
    int x = 1;
    try {
        return x;
    } finally {
        x = 2;      // too late — return value was already evaluated/copied
    }
}
// test2() returns 1, not 2
```

**Takeaway:** The return value is computed and "locked in" before `finally` runs, unless `finally` itself explicitly returns or throws, in which case it overrides everything.

Also: if `finally` contains a `return`, any exception pending from `try`/`catch` is **swallowed** — this is considered bad practice.

---

## 7. throw vs throws

- `throw` — a statement used to actually throw an exception instance: `throw new IllegalArgumentException("bad input");`
- `throws` — a method-signature declaration listing checked exceptions the method might propagate: `void readFile() throws IOException`

---

## 8. Exception Chaining (Root Cause Preservation)

When you catch one exception and throw a different, more meaningful one, always preserve the original as the **cause** — don't swallow it.

```java
try {
    connectToDb();
} catch (SQLException e) {
    throw new ServiceException("Could not initialize service", e); // e is the cause
}
```

Later, `getCause()` retrieves the original exception, and the stack trace shows "Caused by: ...".

---

## 9. Custom Exceptions

```java
public class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
    public InsufficientFundsException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

Guidelines:

- Extend `Exception` for a checked custom exception (caller must handle), or `RuntimeException` for unchecked.
- Always provide constructors that accept `(String message)` and `(String message, Throwable cause)`.
- Name it ending in `Exception` by convention.
- Add extra fields (e.g., an error code) if the caller needs structured data, not just a message.

---

## 10. Common Built-in Exceptions — Know What Triggers Them

|Exception|Typical Cause|
|---|---|
|`NullPointerException`|Calling a method/field on a `null` reference|
|`ArrayIndexOutOfBoundsException`|Array index < 0 or >= length|
|`ArithmeticException`|Integer division by zero (`5/0`) — note: `5.0/0` gives `Infinity`, no exception|
|`ClassCastException`|Invalid downcast, e.g., `Object o = "hi"; Integer i = (Integer) o;`|
|`NumberFormatException`|`Integer.parseInt("abc")`|
|`IllegalArgumentException`|Method receives an invalid argument value|
|`IllegalStateException`|Method called at an inappropriate time (e.g., `Iterator.remove()` before `next()`)|
|`ConcurrentModificationException`|Structurally modifying a collection while iterating without `Iterator.remove()`|
|`UnsupportedOperationException`|Calling a mutator on an immutable collection, e.g., `List.of(1,2).add(3)`|
|`StackOverflowError`|Infinite/too-deep recursion|
|`OutOfMemoryError`|Heap exhausted|

---

## 11. Exception Handling Best Practices (frequently asked as "how do you handle exceptions well?")

1. **Catch specific exceptions, not `Exception` or `Throwable`** broadly — you lose the ability to react appropriately and may swallow bugs like `NullPointerException`.
2. **Never leave a catch block empty.** At minimum, log it.
3. **Don't use exceptions for normal control flow** — they're expensive (stack trace capture) and hurt readability.
4. **Fail fast** — validate inputs early and throw immediately rather than letting bad state propagate.
5. **Preserve the cause** when wrapping/rethrowing (exception chaining, above).
6. **Clean up resources** with try-with-resources rather than manual `finally` blocks.
7. **Document checked exceptions** in Javadoc (`@throws`).
8. **Don't catch `Error`** — it typically signals unrecoverable JVM-level problems.
9. Prefer **unchecked exceptions for API design** in modern Java (this is debated — see below) since checked exceptions can lead to boilerplate `try/catch` pyramids or overly broad `throws Exception`.

---

## 12. Checked vs Unchecked — The Design Debate (good for "why" questions)

- **Argument for checked exceptions:** Forces callers to consciously handle recoverable failure (e.g., a missing file) — the compiler is a safety net.
- **Argument against:** Leads to boilerplate, `throws Exception` escape hatches, and breaks functional-style code (lambdas can't easily throw checked exceptions). Many modern frameworks (Spring, Hibernate) deliberately use unchecked exceptions everywhere for this reason.

Be ready to say: _"I use checked exceptions for recoverable, expected conditions the caller must handle, and unchecked exceptions for programming errors or conditions that shouldn't normally be caught locally."_

---

## 13. Stack Traces

```java
try {
    doSomething();
} catch (Exception e) {
    e.printStackTrace();       // prints to System.err
    // better in production:
    logger.error("Failed", e); // logging framework captures stack trace too
}
```

`getStackTrace()` returns a `StackTraceElement[]` you can inspect programmatically (class, method, line number).

---

## 14. finally Execution Edge Cases

- `finally` does **not** run if the JVM exits via `System.exit()` inside `try`.
- `finally` does **not** run if the thread is killed abruptly (rare, `Thread.stop()` — deprecated anyway).
- `finally` **does** run even if `catch` itself throws a new exception (finally still executes, then the new exception propagates).

---

## 15. Quick-Fire Interview Q&A

**Q: Can you catch multiple exceptions in one catch block?** Yes, with multi-catch (`catch (A | B e)`), introduced in Java 7.

**Q: Can a `finally` block throw an exception?** Yes — and if it does, it silently replaces any exception from `try`/`catch`.

**Q: What's the difference between `Exception` and `Error`?** `Exception` represents conditions an application might reasonably want to catch and recover from. `Error` represents serious problems (JVM/environment level) that applications generally should not try to catch.

**Q: Is `finally` guaranteed to execute?** Almost always — exceptions are `System.exit()`, JVM crash, or the thread being killed.

**Q: Can you have `try` without `catch`?** Yes, `try-finally` is valid, and `try-with-resources` alone (no catch, no finally) is also valid.

**Q: What happens if you don't catch a checked exception and don't declare `throws`?** Compile-time error.

**Q: What happens if an unchecked exception is never caught?** The JVM terminates that thread, printing the stack trace (main thread termination usually ends the program).

**Q: Difference between `final`, `finally`, `finalize()`?**

- `final` — keyword for constants/non-overridable methods/non-extendable classes.
- `finally` — block that always executes after try/catch.
- `finalize()` — deprecated `Object` method the GC used to call before reclaiming an object (removed in Java 18; don't rely on it).

**Q: Can a constructor throw an exception?** Yes, both checked and unchecked.

**Q: What is exception propagation?** If a method doesn't catch an exception, it propagates up the call stack to the caller, and so on, until caught or it reaches `main()` and terminates the program.

**Q: Difference between `throw new Exception("msg")` and `throw new Exception("msg", cause)`?** The second preserves the original exception as the cause, visible in the stack trace as "Caused by:".

**Q: Why can't lambdas easily throw checked exceptions?** Functional interfaces like `Runnable` or `Function` don't declare checked exceptions in their method signatures, so a lambda body throwing a checked exception won't compile unless you wrap/rethrow as unchecked or use a custom functional interface that declares `throws`.

---

## 16. A Short Code Example Worth Having Ready

```java
public class BankAccount {
    private double balance;

    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        if (amount > balance) {
            throw new InsufficientFundsException(
                "Cannot withdraw " + amount + ", balance is " + balance);
        }
        balance -= amount;
    }
}

class InsufficientFundsException extends Exception {
    public InsufficientFundsException(String message) {
        super(message);
    }
}
```

This shows: input validation with unchecked exceptions, a business-rule violation with a checked custom exception, and proper `throws` declaration.

---

## 17. One-Line Summary to Say Out Loud in the Interview

_"Java exceptions extend `Throwable`; `Error` is for unrecoverable JVM issues, `Exception` splits into checked (compiler-enforced, for recoverable/external conditions) and unchecked `RuntimeException`s (for programming bugs). I use try-with-resources for cleanup, multi-catch for related handling, chain exceptions to preserve root cause, and avoid catching broadly or using exceptions for control flow."_



Absolutely. Since you're revising Java for interviews rather than learning programming from zero, I'm going to treat **exceptions as a Java/JVM + interview topic**, not just “here is `try-catch`.”

Your goal should be to understand **why Java exceptions work the way they do, what happens at runtime, what the hierarchy means, and what code actually does**.

Keep your file as the playground:

```
package exceptions;

public class Exceptions {
    public static void main(String[] args) {

    }
}
```

# Java Exceptions — Interview-Ready

## 1. First: what exactly is an exception?

An exception is an **object representing an abnormal condition during program execution**.

For example:

```
int x = 10 / 0;
```

Java cannot perform the division, so an object representing that failure is created/thrown:

```
ArithmeticException
```

Conceptually:

```
Your code
   ↓
something goes wrong
   ↓
exception object
   ↓
thrown
   ↓
JVM looks for a handler
   ↓
catch block
```

The important word is **object**.

An exception isn't just a message like `"division by zero"`.

It is an actual object containing information such as:

- exception type
- message
- cause
- stack trace
- suppressed exceptions

---

# 2. The entire exception hierarchy

This is one of the things you absolutely should know.

```
Object
  └── Throwable
       ├── Error
       │    ├── OutOfMemoryError
       │    ├── StackOverflowError
       │    └── ...
       │
       └── Exception
            ├── RuntimeException
            │    ├── NullPointerException
            │    ├── ArithmeticException
            │    ├── IndexOutOfBoundsException
            │    ├── IllegalArgumentException
            │    └── ...
            │
            └── Other checked exceptions
                 ├── IOException
                 ├── SQLException
                 ├── InterruptedException
                 └── ...
```

The key structure:

```
Throwable
├── Error
└── Exception
    └── RuntimeException
```

This distinction is **extremely important in interviews**.

---

# 3. `Throwable`

`Throwable` is the root type for things that can be thrown and caught.

```
public class Throwable
```

Both:

```
Error
```

and

```
Exception
```

inherit from it.

Therefore:

```
catch (Throwable t) {
}
```

can theoretically catch both exceptions and errors.

But **don't normally do this**.

We'll come back to why.

---

# 4. Exception vs Error

This is a very common interview question.

## Exception

Generally represents conditions that an application may reasonably handle.

Examples:

```
IOException
SQLException
InterruptedException
NullPointerException
IllegalArgumentException
```

## Error

Generally represents serious problems associated with the JVM/runtime/environment.

Examples:

```
OutOfMemoryError
StackOverflowError
NoClassDefFoundError
```

For example:

```
public static void recurse() {
    recurse();
}
```

Eventually:

```
StackOverflowError
```

You normally don't write:

```
try {
    recurse();
} catch (StackOverflowError e) {
}
```

and pretend everything is fine.

### Interview answer

> `Exception` generally represents conditions an application can potentially handle, while `Error` represents serious JVM or system-level problems that applications generally shouldn't attempt to recover from.

Don't say **“Errors are never catchable.”**

They technically can be caught.

```
try {
    ...
} catch (OutOfMemoryError e) {
    ...
}
```

It's just generally inappropriate to treat them as ordinary application exceptions.

---

# 5. Checked vs unchecked exceptions

This is probably the **single most important exception distinction for interviews**.

The division is:

```
Exception
├── RuntimeException
│    └── unchecked
│
└── other Exception subclasses
     └── checked
```

## Checked exception

A checked exception is an exception that the compiler requires you to **handle or declare**.

Example:

```
import java.io.IOException;

public static void readFile() throws IOException {
}
```

Or:

```
try {
    readFile();
} catch (IOException e) {
}
```

The compiler forces you to deal with it.

---

# 6. Unchecked exceptions

Unchecked exceptions are:

```
RuntimeException
and its subclasses
```

Example:

```
int x = 10 / 0;
```

Produces:

```
ArithmeticException
```

But Java doesn't force you to write:

```
try {
    int x = 10 / 0;
} catch (ArithmeticException e) {
}
```

Why?

Because `ArithmeticException` is unchecked.

Other examples:

```
NullPointerException
IllegalArgumentException
IllegalStateException
IndexOutOfBoundsException
ClassCastException
NumberFormatException
ConcurrentModificationException
```

---

# 7. Why does Java have checked exceptions?

This is an excellent conceptual interview question.

Suppose:

```
Files.readString(path);
```

Reading a file can fail for reasons outside your program:

- file doesn't exist
- permissions
- filesystem failure
- etc.

Java therefore forces the programmer to acknowledge that possibility.

Conceptually:

> Checked exceptions represent failures that the compiler wants you to explicitly account for.

Whereas:

```
String name = null;
name.length();
```

A `NullPointerException` is usually caused by a programming error.

Java doesn't force:

```
try {
    name.length();
}
```

around every line.

---

# 8. `try`

A `try` block contains code that might throw an exception.

```
try {
    int result = 10 / 0;
}
```

But `try` by itself isn't enough.

You need either:

```
catch
```

or:

```
finally
```

or both.

---

# 9. `catch`

```
try {
    int result = 10 / 0;
} catch (ArithmeticException e) {
    System.out.println("Cannot divide by zero");
}
```

The variable:

```
e
```

is the actual exception object.

You can inspect it:

```
System.out.println(e.getMessage());
System.out.println(e.getClass());
e.printStackTrace();
```

---

# 10. `finally`

`finally` is generally used for cleanup.

```
try {
    System.out.println("try");
} catch (Exception e) {
    System.out.println("catch");
} finally {
    System.out.println("finally");
}
```

Normally:

```
try
finally
```

If exception occurs and is caught:

```
try
catch
finally
```

If exception occurs and isn't caught:

```
try
finally
exception propagates
```

---

# 11. Does `finally` ALWAYS execute?

This is a classic trick question.

**No.**

Usually it executes, but there are situations where it doesn't.

For example:

```
try {
    System.exit(0);
} finally {
    System.out.println("hello");
}
```

The JVM terminates, so `finally` doesn't get to execute normally.

Likewise, catastrophic JVM/process termination can prevent it.

So don't say:

> "`finally` always executes."

Say:

> "`finally` normally executes when control leaves the `try`/`catch`, including during exception propagation, but it is not guaranteed under situations such as JVM termination."

---

# 12. The most important runtime concept: stack unwinding

Understand this and exception handling becomes much easier.

Suppose:

```
static void a() {
    b();
}

static void b() {
    c();
}

static void c() {
    int x = 10 / 0;
}
```

Execution:

```
main()
  ↓
a()
  ↓
b()
  ↓
c()
  ↓
ArithmeticException
```

There is no handler inside `c`.

JVM goes backward through the call stack looking for a matching handler.

```
c()  ← no handler
 ↓
b()  ← no handler
 ↓
a()  ← no handler
 ↓
main() ← maybe handler
```

This is called **stack unwinding**.

This is one of the most important concepts to understand rather than memorize.

---

# 13. Exception propagation

Example:

```
public static void main(String[] args) {

    try {
        methodA();
    } catch (ArithmeticException e) {
        System.out.println("Handled");
    }
}

static void methodA() {
    methodB();
}

static void methodB() {
    int x = 10 / 0;
}
```

The exception originates in:

```
methodB()
```

but gets handled in:

```
main()
```

because it propagates up the call stack.

---

# 14. `throw`

`throw` means:

> Actually throw an exception object.

Example:

```
throw new IllegalArgumentException("Age cannot be negative");
```

You can create your own validation:

```
static void setAge(int age) {

    if (age < 0) {
        throw new IllegalArgumentException("Age cannot be negative");
    }
}
```

Very important distinction:

```
throw
```

vs

```
throws
```

---

# 15. `throws`

`throws` appears in a method declaration.

```
static void readFile() throws IOException {
}
```

It means:

> This method may allow this exception to propagate to its caller.

It doesn't itself throw anything.

Think:

```
throw  → action
throws → declaration
```

Example:

```
throw new IOException();
```

vs:

```
void read() throws IOException
```

---

# 16. Interview trap: does `throws` throw the exception?

No.

This:

```
void test() throws IOException {
}
```

doesn't throw an exception by itself.

It says callers need to account for the possibility of `IOException`.

---

# 17. Multiple catch blocks

You can have:

```
try {
    // risky code
} catch (ArithmeticException e) {
    ...
} catch (NullPointerException e) {
    ...
} catch (Exception e) {
    ...
}
```

Order matters.

This is invalid:

```
catch (Exception e) {
}
catch (ArithmeticException e) {
}
```

because `ArithmeticException` is already caught by `Exception`.

Compiler error: unreachable catch block.

Think inheritance:

```
Exception
   ↑
ArithmeticException
```

You must catch the child before the parent.

---

# 18. Multi-catch

Java allows:

```
try {
    ...
} catch (IOException | SQLException e) {
    ...
}
```

Useful when handling multiple unrelated exception types identically.

One interesting restriction:

You cannot have related types:

```
catch (Exception | IOException e)
```

because `IOException` is already a subtype of `Exception`.

---

# 19. `Exception` catch vs specific exception

You can do:

```
catch (Exception e)
```

but don't automatically do that everywhere.

Prefer:

```
catch (IOException e)
```

when you know what you're handling.

Why?

Because the handling logic should correspond to the actual failure.

Bad:

```
try {
    database.save();
} catch (Exception e) {
    System.out.println("Something went wrong");
}
```

This can hide programming bugs.

---

# 20. Catching `Throwable`

Technically:

```
catch (Throwable t)
```

catches almost everything throwable.

But it's usually a terrible idea.

It catches:

```
Exception
Error
```

including serious things such as:

```
OutOfMemoryError
StackOverflowError
```

Normally you don't want your application to pretend those are ordinary recoverable exceptions.

---

# 21. Exception messages

You can create:

```
throw new IllegalArgumentException("Age must be positive");
```

Then:

```
catch (IllegalArgumentException e) {
    System.out.println(e.getMessage());
}
```

Output:

```
Age must be positive
```

---

# 22. Stack trace

This:

```
e.printStackTrace();
```

prints information showing where the exception occurred and how execution got there.

Example conceptually:

```
java.lang.ArithmeticException: / by zero
    at exceptions.Exceptions.calculate(Exceptions.java:15)
    at exceptions.Exceptions.main(Exceptions.java:7)
```

Read it **from the top downward**.

The first application frame is generally where the exception was thrown.

---

# 23. `getMessage()` vs `toString()` vs `printStackTrace()`

Interviewers love this sort of thing.

```
e.getMessage()
```

→ just the message.

```
e.toString()
```

→ exception class + message.

Something conceptually like:

```
java.lang.ArithmeticException: / by zero
```

```
e.printStackTrace()
```

→ detailed stack trace.

---

# 24. Exception constructors

Many exception classes support:

```
new Exception()
```

```
new Exception("message")
```

```
new Exception(cause)
```

```
new Exception("message", cause)
```

This leads to an important concept:

# Exception chaining

Suppose your application gets:

```
SQLException
```

You may want to expose an application-specific exception:

```
throw new BookingException(
    "Unable to create booking",
    e
);
```

The original exception becomes the **cause**.

Then:

```
e.getCause()
```

can retrieve it.

---

# 25. Why exception chaining exists

Imagine:

```
Database
   ↓
SQLException
   ↓
Repository
   ↓
BookingRepositoryException
   ↓
Service
   ↓
BookingServiceException
```

Each layer can add context without losing the original failure.

This is extremely useful in real backend applications.

---

# 26. Custom exceptions

You can create your own exception.

Unchecked:

```
class InvalidBookingException extends RuntimeException {

    public InvalidBookingException(String message) {
        super(message);
    }
}
```

Then:

```
throw new InvalidBookingException("Room is already booked");
```

Checked:

```
class BookingException extends Exception {

    public BookingException(String message) {
        super(message);
    }
}
```

Difference:

```
extends Exception
```

→ checked

```
extends RuntimeException
```

→ unchecked.

---

# 27. When should your custom exception be checked?

This is more design-oriented.

Use checked exceptions when callers are genuinely expected to **recover or explicitly handle a condition**.

Use unchecked exceptions when the condition is generally a **programming error, invalid state, or something the caller isn't reasonably expected to recover from at every call site**.

Modern Java backend code tends to use unchecked exceptions heavily.

---

# 28. `try-with-resources`

This is **very important**.

Before try-with-resources:

```
BufferedReader reader = null;

try {
    reader = new BufferedReader(...);
} finally {
    if (reader != null) {
        reader.close();
    }
}
```

Java 7 introduced:

```
try (BufferedReader reader = new BufferedReader(...)) {
    // use reader
}
```

Java automatically closes the resource.

The resource must implement:

```
AutoCloseable
```

or its subinterface:

```
Closeable
```

---

# 29. Why try-with-resources is powerful

Consider:

```
try (BufferedReader reader = ...) {
    ...
}
```

When the block ends, Java automatically invokes:

```
reader.close();
```

Even when an exception occurs.

This dramatically reduces resource-leak bugs.

---

# 30. Multiple resources

You can do:

```
try (
    BufferedReader reader = ...;
    BufferedWriter writer = ...
) {
    ...
}
```

They are closed automatically.

And the closing order is **reverse declaration order**:

```
writer closes
reader closes
```

Think stack:

```
reader opened
writer opened
   ↓
writer closes
reader closes
```

---

# 31. Suppressed exceptions

This is a surprisingly good interview topic.

Imagine:

```
try (Resource resource = new Resource()) {
    throw new Exception("main failure");
}
```

Then:

```
resource.close();
```

also throws an exception.

Which one should win?

The exception from the `try` body becomes the primary exception.

The exception from `close()` becomes a **suppressed exception**.

You can inspect them:

```
Throwable[] suppressed = e.getSuppressed();
```

This is one of the reasons try-with-resources is much better than manually writing cleanup code.

---

# 32. `finally` and `return`

Another classic interview trap.

Look at:

```
static int test() {
    try {
        return 10;
    } finally {
        System.out.println("finally");
    }
}
```

What happens?

```
finally
```

prints, and the method returns:

```
10
```

The `finally` block executes before the method actually completes.

---

# 33. The nasty `return` in finally

Look at:

```
static int test() {
    try {
        return 10;
    } finally {
        return 20;
    }
}
```

Result:

```
20
```

The `finally` return overrides the previous return.

This is legal Java.

But **never write code like this in production**.

It destroys clarity and can swallow exceptions.

---

# 34. Even worse: exception swallowed by finally

```
static void test() {
    try {
        throw new RuntimeException("boom");
    } finally {
        return;
    }
}
```

The exception is effectively swallowed because `finally` returns.

This is why:

> **Never return from a `finally` block.**

Excellent interview knowledge.

---

# 35. `finally` can modify variables

Be careful with local variables and returns.

For example:

```
static int test() {
    int x = 10;

    try {
        return x;
    } finally {
        x = 20;
    }
}
```

This returns:

```
10
```

The return value has already been determined before `finally` modifies the local variable.

But reference/object mutation can make things more interesting.

That's worth understanding conceptually, but don't overfocus on obscure puzzles.

---

# 36. `final`, `finally`, `finalize`

Classic interview question.

### `final`

Keyword.

```
final int x = 10;
```

### `finally`

Exception-handling block.

```
finally {
}
```

### `finalize`

Old object-finalization mechanism associated with garbage collection.

It has been **deprecated for removal** in modern Java and should not be used for resource management.

So if an interviewer asks:

> Difference between final, finally and finalize?

Know all three, but understand that `finalize()` is legacy/deprecated territory.

---

# 37. Exception handling and method overriding

This is a **very important OOP + exception interview topic**.

Suppose parent:

```
class Parent {
    void test() throws IOException {
    }
}
```

Child cannot do:

```
class Child extends Parent {
    void test() throws Exception {
    }
}
```

Why?

Because `Exception` is broader than `IOException`.

A caller holding a `Parent` reference is only expecting the contract:

```
throws IOException
```

The child cannot expand that checked-exception contract.

---

# 38. What CAN an overriding method throw?

If parent says:

```
void test() throws IOException
```

child can:

### Throw same exception

```
void test() throws IOException
```

### Throw subclass

```
void test() throws FileNotFoundException
```

because:

```
IOException
   ↑
FileNotFoundException
```

### Throw nothing

```
void test()
```

### Throw unchecked exceptions

```
void test() throws RuntimeException
```

Unchecked exceptions don't participate in the checked-exception restriction.

This is a **must-know interview rule**.

---

# 39. Constructors and exceptions

Constructors can throw exceptions:

```
class User {

    User(String name) throws IOException {
    }
}
```

Then:

```
User user = new User("Bibek");
```

must handle/declare the checked exception.

---

# 40. Static initialization and exceptions

This is more advanced but worth knowing.

If a static initializer throws an exception:

```
class Test {

    static {
        if (true) {
            throw new RuntimeException();
        }
    }
}
```

class initialization can fail.

For unchecked exceptions during class initialization, the JVM can wrap the failure in:

```
ExceptionInInitializerError
```

And subsequent attempts to initialize/use the class can result in:

```
NoClassDefFoundError
```

You don't need to memorize every detail initially, but recognize these names.

---

# 41. Exceptions don't necessarily mean “bug”

Important distinction.

Some exceptions represent expected environmental situations.

Example:

```
FileNotFoundException
```

The program isn't necessarily buggy because a file doesn't exist.

Likewise:

```
SocketTimeoutException
```

doesn't necessarily mean your program is broken.

But:

```
NullPointerException
```

often indicates a programming mistake.

The exception's meaning depends on context.

---

# 42. Don't use exceptions for normal control flow

Bad:

```
try {
    int x = array[index];
} catch (IndexOutOfBoundsException e) {
    // means index doesn't exist
}
```

Better:

```
if (index >= 0 && index < array.length) {
    ...
}
```

Exceptions are relatively expensive and, more importantly, using them for ordinary branching makes code harder to understand.

---

# 43. Don't swallow exceptions

This is horrible:

```
try {
    something();
} catch (Exception e) {
}
```

Now the failure disappears.

You don't know:

- what failed
- why it failed
- where it failed

At minimum, handle it meaningfully or propagate it.

---

# 44. Logging and rethrowing

Be careful with:

```
catch (Exception e) {
    log.error("Something failed", e);
    throw e;
}
```

This is sometimes useful.

But if every layer does this:

```
repository logs
service logs
controller logs
global handler logs
```

you get duplicate logs.

In real backend systems, decide **where an exception should be logged and where it should be translated/handled**.

---

# 45. Preserve the cause

Bad:

```
catch (SQLException e) {
    throw new BookingException("Booking failed");
}
```

You've lost the original cause.

Better:

```
catch (SQLException e) {
    throw new BookingException("Booking failed", e);
}
```

Now:

```
e.getCause()
```

still points to the original exception.

This is extremely relevant in backend development.

---

# 46. `throw e` vs `throw new Exception(e)`

These are not necessarily equivalent.

```
catch (IOException e) {
    throw e;
}
```

preserves the same exception object.

Whereas:

```
catch (IOException e) {
    throw new RuntimeException(e);
}
```

creates a new exception whose cause is the original.

That's often exactly what you want when crossing an architectural boundary.

---

# 47. `getCause()`

Example:

```
Exception original = new Exception("Database failed");

RuntimeException wrapped =
        new RuntimeException("Service failed", original);
```

Then:

```
wrapped.getCause()
```

returns:

```
original
```

This creates an exception chain.

---

# 48. `initCause()`

You can also establish a cause afterward:

```
Exception e = new Exception("Something failed");

e.initCause(originalException);
```

Usually constructors accepting a cause are cleaner.

---

# 49. Exception suppression

You can manually suppress an exception:

```
exception.addSuppressed(otherException);
```

And retrieve:

```
exception.getSuppressed();
```

Most commonly you'll encounter suppressed exceptions through try-with-resources.

---

# 50. `getStackTrace()`

You can retrieve the stack trace programmatically:

```
StackTraceElement[] trace = e.getStackTrace();
```

Each element contains information such as:

```
getClassName()
getMethodName()
getFileName()
getLineNumber()
```

Useful for logging/debugging tools.

---

# 51. `fillInStackTrace()`

`Throwable` can capture the current stack trace.

There is:

```
fillInStackTrace()
```

This is more advanced and generally isn't something you need to manually use in normal application code.

But recognize it if you encounter it while reading Java internals/framework code.

---

# 52. Exception performance

A common misconception:

> “Exceptions are slow because Java uses exceptions.”

The more precise statement is:

Creating/throwing exceptions can be relatively expensive, particularly because stack-trace information may be captured.

Therefore don't do:

```
for (...) {
    try {
        ...
    } catch (...) {
    }
}
```

as your normal branching mechanism.

But don't obsess about micro-optimizing exceptions either.

The primary concern is **correctness and design**.

---

# 53. Compile-time vs runtime exceptions

Example:

```
try {
    Files.readString(path);
} catch (IOException e) {
}
```

The compiler checks the checked exception.

But:

```
String s = null;
s.length();
```

The compiler doesn't force you to handle `NullPointerException`.

It occurs at runtime.

So:

```
Checked exception
    ↓
compiler participates

Unchecked exception
    ↓
compiler doesn't require handling
```

---

# 54. Can you catch an exception that cannot happen?

Interesting compiler behavior.

For checked exceptions, Java's compiler analyzes whether the exception can be thrown from the relevant `try` block.

For example, a catch block for a checked exception may be rejected if no statement in the try can throw it.

But unchecked exceptions don't have this restriction.

That's because `RuntimeException` subclasses can arise from ordinary runtime execution.

---

# 55. `catch` matching uses inheritance

Suppose:

```
try {
    ...
} catch (IOException e) {
}
```

and actual exception is:

```
FileNotFoundException
```

Will it catch?

Yes.

Because:

```
IOException
    ↑
FileNotFoundException
```

The catch parameter can refer to a superclass of the actual exception.

This is ordinary polymorphism.

---

# 56. The first matching catch executes

Example:

```
try {
    ...
} catch (FileNotFoundException e) {
    ...
} catch (IOException e) {
    ...
}
```

If `FileNotFoundException` occurs:

```
FileNotFoundException catch
```

executes.

Java doesn't continue into the next catch afterward.

Only one matching catch block handles that exception.

---

# 57. Exception inside catch

You can throw another exception from a catch:

```
try {
    ...
} catch (IOException e) {
    throw new RuntimeException("Could not process file", e);
}
```

The new exception propagates from there.

This is very common in layered applications.

---

# 58. Exception inside finally

You can technically do:

```
try {
    ...
} finally {
    throw new RuntimeException("cleanup failed");
}
```

If another exception was already being propagated, the exception from `finally` can replace/suppress the original flow.

This is another reason not to put risky operations or `throw`/`return` logic carelessly inside `finally`.

---

# 59. Try without catch

Valid:

```
try {
    something();
} finally {
    cleanup();
}
```

So `try` doesn't necessarily require `catch`.

---

# 60. Catch without try?

Invalid.

```
catch (Exception e) {
}
```

must belong to a `try`.

---

# 61. Finally without try?

Invalid.

```
finally {
}
```

must be associated with a `try`.

---

# 62. Nested try/catch

Possible:

```
try {

    try {
        ...
    } catch (Exception e) {
        ...
    }

} catch (Exception e) {
    ...
}
```

The inner handler gets the first opportunity to handle its exception.

If it doesn't handle it, propagation continues outward.

---

# 63. `try` inside `catch`

Also legal:

```
try {
    ...
} catch (Exception e) {

    try {
        ...
    } catch (Exception another) {
        ...
    }
}
```

But don't write bizarre nesting just because Java allows it.

---

# 64. Resource cleanup and exceptions

For backend interviews, think about:

```
File
Database connection
Socket
HTTP connection
Stream
Lock/resource
```

The general rule:

> Resources need deterministic cleanup.

That's why:

```
try-with-resources
```

is so important.

---

# 65. `AutoCloseable`

Try-with-resources works with:

```
AutoCloseable
```

which essentially defines:

```
void close() throws Exception;
```

Example:

```
class MyResource implements AutoCloseable {

    @Override
    public void close() {
        System.out.println("closed");
    }
}
```

Then:

```
try (MyResource resource = new MyResource()) {
    System.out.println("using");
}
```

Output:

```
using
closed
```

---

# 66. Checked exception and lambda

This can become important in Java interviews.

Functional interfaces can declare checked exceptions or not.

For example:

```
Runnable
```

has:

```
void run();
```

It does **not** declare checked exceptions.

So:

```
Runnable r = () -> {
    throw new IOException(); // doesn't compile
};
```

unless you handle it inside the lambda or use an appropriate functional interface.

This is one reason checked exceptions can become awkward with Java functional APIs.

---

# 67. Exception handling with streams

For example:

```
files.stream()
     .map(path -> Files.readString(path))
```

`Files.readString()` throws a checked exception.

But `Function<T,R>` doesn't allow a checked exception in its `apply()` declaration.

Therefore you can't simply do this without handling/wrapping it.

This is a real-world Java pain point worth recognizing.

---

# 68. Exceptions and threads

Each thread has its own execution stack.

If a thread throws an exception that isn't handled, it doesn't magically get caught by another thread's `try/catch`.

For example:

```
try {
    new Thread(() -> {
        throw new RuntimeException();
    }).start();
} catch (RuntimeException e) {
    System.out.println("caught");
}
```

The outer `catch` does **not** catch the exception thrown by the new thread.

Why?

Because the exception belongs to that thread's execution.

This connects directly to the thread topics you've been studying.

---

# 69. Uncaught exceptions

If a thread terminates because an exception wasn't caught, Java's uncaught-exception mechanism can handle/report it.

You can configure an:

```
UncaughtExceptionHandler
```

Example:

```
Thread.setDefaultUncaughtExceptionHandler(
    (thread, exception) -> {
        System.out.println(
            thread.getName() + " failed"
        );
    }
);
```

This is useful in concurrent applications.

---

# 70. Exception handling doesn't “resume” execution

Suppose:

```
try {
    System.out.println("A");
    int x = 10 / 0;
    System.out.println("B");
} catch (ArithmeticException e) {
    System.out.println("C");
}

System.out.println("D");
```

Output:

```
A
C
D
```

Not:

```
A
C
B
D
```

Once the exception occurs, normal execution of that `try` block stops.

The JVM transfers control to the matching handler.

---

# 71. One very important mental model

Think:

```
throw
  ↓
STOP normal execution
  ↓
search current method for handler
  ↓
if none:
    leave method
  ↓
caller
  ↓
search caller
  ↓
continue upward
  ↓
handler found?
    yes → catch
    no  → thread terminates
```

That's exception propagation.

---

# 72. The `main` method can declare exceptions

You can write:

```
public static void main(String[] args) throws Exception {
}
```

This is legal.

It means `main` isn't handling that exception; it allows it to propagate to the JVM.

You'll see this frequently in small programs/tests.

---

# 73. Should you always use `throws Exception`?

No.

This:

```
void process() throws Exception
```

throws away useful information.

Prefer:

```
void process() throws IOException
```

when that's the actual contract.

Broad exception declarations make APIs less precise.

---

# 74. Exception hierarchy design

You can build your own hierarchy:

```
BookingException
├── RoomUnavailableException
├── InvalidBookingException
└── PaymentException
```

Then you can catch specifically:

```
catch (RoomUnavailableException e)
```

or broadly:

```
catch (BookingException e)
```

This is one of the strengths of exceptions being classes.

---

# 75. Don't expose low-level exceptions unnecessarily

Imagine:

```
Controller
    ↓
Service
    ↓
Repository
    ↓
JDBC
```

Your controller probably doesn't need to know about every:

```
SQLException
```

Your repository/service layer can translate it into something meaningful to the domain:

```
throw new BookingException(
    "Unable to create booking",
    sqlException
);
```

This is exception abstraction.

---

# 76. Exception handling architecture

A mature backend often looks conceptually like:

```
Low-level failure
      ↓
Repository
      ↓
translate / preserve cause
      ↓
Service
      ↓
domain exception
      ↓
Controller
      ↓
global exception handler
      ↓
HTTP response
```

For example:

```
SQLException
    ↓
BookingPersistenceException
    ↓
BookingException
    ↓
HTTP 409 / 500
```

The exact mapping depends on the application.

This is much more important for your backend interviews than memorizing 50 exception names.

---

# 77. `RuntimeException` doesn't mean “runtime errors only”

Don't interpret the name literally.

`RuntimeException` means a particular class in the exception hierarchy:

```
java.lang.RuntimeException
```

Its subclasses are unchecked.

It doesn't mean:

> an exception that happens at runtime.

Obviously every exception happens during execution.

---

# 78. Common exceptions you should recognize

You don't need to memorize every JDK exception.

Know these:

### Null/reference problems

```
NullPointerException
```

### Invalid arguments

```
IllegalArgumentException
```

### Invalid state

```
IllegalStateException
```

### Numeric

```
ArithmeticException
NumberFormatException
```

### Collections/arrays

```
IndexOutOfBoundsException
ArrayIndexOutOfBoundsException
ConcurrentModificationException
```

### Casting

```
ClassCastException
```

### I/O

```
IOException
FileNotFoundException
EOFException
```

### Concurrency

```
InterruptedException
```

### Reflection/class loading

```
ClassNotFoundException
NoClassDefFoundError
ExceptionInInitializerError
```

Know what these generally mean, not every constructor.

---

# 79. `ClassNotFoundException` vs `NoClassDefFoundError`

This is a good interview question.

### `ClassNotFoundException`

A checked exception.

Typically occurs when code explicitly tries to load a class and it cannot be found.

Example:

```
Class.forName("com.example.SomeClass");
```

### `NoClassDefFoundError`

An `Error`.

Generally indicates the JVM cannot find/load a class definition that was expected to be available.

They're related conceptually but not the same thing.

---

# 80. `ExceptionInInitializerError`

Remember the relationship:

```
class initialization
       ↓
static initialization failure
       ↓
ExceptionInInitializerError
```

This is a good advanced Java-reading fact.

---

# 81. `InterruptedException` is special

Since you're studying threads now, pay attention to this one.

A blocking operation may throw:

```
InterruptedException
```

For example:

```
Thread.sleep(1000);
```

The correct response isn't automatically:

```
catch (InterruptedException e) {
    e.printStackTrace();
}
```

Often you should restore the thread's interrupted status:

```
catch (InterruptedException e) {
    Thread.currentThread().interrupt();
}
```

Why?

Because interruption is a **cooperative cancellation signal**, not merely an error.

We'll go deep into this when you reach the interruption portion of your thread study.

---

# 82. Exception vs assertion

Don't confuse:

```
throw new IllegalArgumentException();
```

with:

```
assert condition;
```

Assertions are primarily development/testing checks and can be disabled at runtime.

Exceptions are part of normal program behavior/error handling.

---

# 83. Assertions aren't exception handling

You don't generally use:

```
assert user != null;
```

as your application's validation mechanism.

Assertions can be disabled.

Use explicit validation when correctness depends on it.

---

# 84. Exception safety and mutable state

Suppose:

```
account.withdraw(100);
account.save();
```

If:

```
withdraw()
```

succeeds but:

```
save()
```

fails, your application can be left in a problematic state.

This is where exception handling intersects with:

- transactions
- rollback
- database consistency
- resource management
- concurrency

For backend interviews, this is much more valuable than exception trivia.

---

# 85. Exceptions and transactions

Suppose:

```
beginTransaction();

try {
    createUser();
    createBooking();
    chargePayment();

    commit();
} catch (Exception e) {
    rollback();
    throw e;
}
```

The exception isn't merely something to print.

It determines the control flow for maintaining system consistency.

Frameworks such as Spring build heavily on this idea.

---

# 86. Don't catch what you can't handle

This is probably the best general design rule.

Bad:

```
try {
    process();
} catch (Exception e) {
    System.out.println("Error");
}
```

Ask:

> What am I actually going to do with this exception?

Possible legitimate answers:

- retry
- return fallback
- translate exception
- rollback
- notify
- return an appropriate HTTP response
- clean up resource
- terminate operation

If you can't meaningfully handle it, often you should let it propagate.

---

# 87. Don't overuse checked exceptions

Checked exceptions aren't automatically “better Java.”

They have tradeoffs:

```
Pros:
- compiler forces awareness
- API communicates recoverable failure

Cons:
- verbose
- propagates through many layers
- awkward with lambdas/streams
- can lead to meaningless catch/rethrow code
```

That's why many modern Java systems use unchecked domain/application exceptions.

---

# 88. The interview puzzle you should be able to solve

Without running it:

```
public static int test() {
    try {
        System.out.println("try");
        return 1;
    } catch (Exception e) {
        System.out.println("catch");
        return 2;
    } finally {
        System.out.println("finally");
    }
}
```

Answer:

```
try
finally
```

return:

```
1
```

Why?

No exception occurs.

The `finally` executes before the method completes.

---

# 89. Another one

```
public static int test() {
    try {
        return 1;
    } finally {
        return 2;
    }
}
```

Return:

```
2
```

Because the `finally` return overrides the pending return.

Again:

**never do this intentionally.**

---

# 90. Another one

```
try {
    throw new RuntimeException("A");
} catch (RuntimeException e) {
    System.out.println("B");
} finally {
    System.out.println("C");
}
```

Output:

```
B
C
```

---

# 91. Another one

```
try {
    throw new RuntimeException("A");
} finally {
    System.out.println("C");
}
```

Output:

```
C
```

then the `RuntimeException` propagates.

---

# 92. Another one

```
try {
    throw new RuntimeException("A");
} catch (RuntimeException e) {
    throw new RuntimeException("B");
}
```

The original exception becomes the cause **only if you explicitly pass it**:

```
throw new RuntimeException("B", e);
```

Without that:

```
throw new RuntimeException("B");
```

you have not preserved the original as the cause.

---

# 93. The interview checklist

If I were preparing you for a difficult Java backend interview, I'd expect you to be comfortable with all of these:

### Core

- What is an exception?
- `Throwable`
- `Exception`
- `RuntimeException`
- `Error`
- checked vs unchecked
- exception hierarchy

### Control flow

- `try`
- `catch`
- `finally`
- multiple catch
- multi-catch
- nested try
- exception propagation
- stack unwinding

### Throwing

- `throw`
- `throws`
- exception constructors
- custom exceptions
- exception chaining
- `getCause()`

### Cleanup

- `finally`
- try-with-resources
- `AutoCloseable`
- resource closing order
- suppressed exceptions

### OOP

- exceptions and inheritance
- overriding rules
- checked exceptions in overriding
- constructors throwing exceptions

### Debugging

- `getMessage()`
- `toString()`
- `printStackTrace()`
- `getStackTrace()`
- cause
- suppressed exceptions

### Concurrency

- exceptions per thread
- uncaught exceptions
- `UncaughtExceptionHandler`
- `InterruptedException`

### Design

- when to catch
- when to propagate
- when to wrap
- preserving causes
- domain exceptions
- avoiding swallowed exceptions
- avoiding exceptions for normal control flow

### Advanced

- class initialization exceptions
- `ExceptionInInitializerError`
- `ClassNotFoundException`
- `NoClassDefFoundError`
- assertions
- exception performance

---

# 94. The 10 questions I would absolutely expect you to answer

Before considering your exception knowledge interview-ready, you should be able to answer these **without notes**:

1. **What is the difference between `Exception`, `RuntimeException`, and `Error`?**
    
2. **What is the difference between checked and unchecked exceptions? Why does Java have checked exceptions?**
    
3. **What is the difference between `throw` and `throws`?**
    
4. **How does exception propagation work? Explain stack unwinding.**
    
5. **Can a child override a method and throw a broader checked exception? Why not?**
    
6. **What happens if both `try` and `finally` contain `return`?**
    
7. **What happens if `try` throws an exception and `finally` throws another exception?**
    
8. **How does try-with-resources work, and what are suppressed exceptions?**
    
9. **Why would you wrap an exception instead of simply throwing the original?**
    
10. **Why doesn't a `try/catch` in one thread catch an exception thrown by another thread?**
    

If you can explain those properly, you're no longer at “I know try-catch” level.

---

# 95. Your `Exceptions.java` should become your laboratory

Don't try to memorize this whole thing.

Use your class to experimentally verify the important behaviors.

I'd structure it like this:

```
package exceptions;

public class Exceptions {

    public static void main(String[] args) {

        // 1. Basic exception
        // basicException();

        // 2. Multiple catch
        // multipleCatch();

        // 3. Finally
        // finallyExample();

        // 4. Throw
        // throwExample();

        // 5. Exception propagation
        // propagationExample();

        // 6. Custom exception
        // customException();

        // 7. Exception chaining
        // chainingExample();

        // 8. Try-with-resources
        // tryWithResources();

        // 9. Suppressed exceptions
        // suppressedExceptions();

        // 10. Finally + return
        // finallyReturn();

        // 11. Thread exception
        // threadException();

    }
}
```

And then implement each method yourself.

**Don't implement all 11 today.** That's exactly how you end up “studying” exceptions without actually understanding them.

I'd do them in this order:

```
Phase 1
  try/catch
  ↓
  finally
  ↓
  throw / throws
  ↓
  propagation + stack unwinding

Phase 2
  checked vs unchecked
  ↓
  custom exceptions
  ↓
  exception chaining
  ↓
  overriding rules

Phase 3
  try-with-resources
  ↓
  AutoCloseable
  ↓
  suppressed exceptions

Phase 4
  thread exceptions
  ↓
  InterruptedException
  ↓
  UncaughtExceptionHandler

Phase 5
  backend exception architecture
  ↓
  translating exceptions
  ↓
  transactions
  ↓
  global exception handling
```

**For your interview preparation, Phase 1–3 are non-negotiable. Phase 4 matters because you're already studying threads. Phase 5 is what separates “Java syntax knowledge” from actual backend Java knowledge.**

And one final memory hook:

```
throw
    ↓
exception object is created/thrown
    ↓
current method searches for handler
    ↓
not found?
    ↓
stack unwinds
    ↓
caller searches
    ↓
...
    ↓
handler found → catch
    ↓
finally
    ↓
continue

No handler anywhere
    ↓
thread terminates
```

If that mental model is solid, most Java exception questions stop being memorization and become **execution-tracing problems**.