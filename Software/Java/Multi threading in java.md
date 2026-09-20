
### Phase 1: `Thread`

Don't move yet until you understand:

- [ ]  What is a process?
- [ ]  What is a thread?
- [ ]  Main thread
- [ ]  `Thread.currentThread()`
- [ ]  Creating a class extending `Thread`
- [ ]  Overriding `run()`
- [ ]  Difference between `run()` and `start()`
- [ ]  Why output order is unpredictable
- [ ]  `Thread.sleep()`
- [ ]  Thread states
- [ ]  `isAlive()`
- [ ]  `join()`
- [ ]  `interrupt()`
- [ ]  What `InterruptedException` means
- [ ]  Why `Thread.currentThread().interrupt()` is commonly used

**Do NOT move to `Runnable` yet.** First master `Thread` itself: lifecycle, `start()`, `run()`, scheduling, sleep, join, interruption, states, daemon threads, synchronization basics, etc.

Answer these questions:

1. Why does `run()` execute on `main` when called directly?
2. Why does `start()` execute on another thread?
3. Does `start()` immediately guarantee execution?
4. Who decides when the new thread actually gets CPU time? // audaina

start doesn't gurantees immediate exection. 

### Stage 1 — raw `Thread`

```
class MyThread extends Thread {
    @Override
    public void run() {
        // work
    }
}

new MyThread().start();
```

Learn:

- Thread object
- `start()`
- `run()`
- thread lifecycle
- scheduling
- `sleep()`
- `join()`
- interruption

```
Thread object
    ↓
Thread lifecycle
    ↓
NEW
    ↓
RUNNABLE
    ↓
sleep()
    ↓
TIMED_WAITING
    ↓
wake up
    ↓
RUNNABLE
    ↓
run() finishes
    ↓
TERMINATED
```

```
- `sleep()` deeply
- `isAlive()`
- `getState()`
- `join()`
- what happens when `run()` finishes
- starting the same `Thread` twice
- what happens when a thread throws an exception
- thread naming
- priority
- daemon vs non-daemon threads
- interruption

**Do not move to `Runnable` or `ExecutorService` yet.** The objective is to make the raw `Thread` model completely intuitive first.
```

You should understand:

- What `Thread` actually is
    
- `Thread` as a Java object
    
- `new Thread()` vs an actual running thread
    
- `Thread` inheritance hierarchy
    
- `extends Thread`
    
- What you inherit from `Thread`
    
- What you can override
    
- What `@Override` means
    
- `run()` and why it exists
    
- `start()` and why it exists
    
- `start()` vs `run()` — **deeply**
    
- Why a `Thread` can only be started once
    
- What happens if you call `start()` twice // a thread instance is only one shot
    
- What happens if you create a `Thread` but never start it
    

---

### 2. Thread lifecycle / states

Know all six Java thread states:

```text
NEW
RUNNABLE
BLOCKED
WAITING
TIMED_WAITING
TERMINATED
```

But don't merely memorize the names.

For each one, understand:

- What causes a thread to enter it
    
- What causes it to leave
    
- Whether it can execute Java code in that state
    
- How `getState()` observes it
    
- Whether the state represents an instantaneous snapshot or a lasting condition
    

Especially understand:

```text
NEW
  ↓ start()
RUNNABLE
  ↓ sleep()
TIMED_WAITING
  ↓ timeout
RUNNABLE
  ↓ run() ends
TERMINATED
```

---

### 3. Scheduling

This is a **big one**.

Understand:

- Java thread vs OS thread
    
- JVM vs operating-system scheduler
    
- CPU cores vs threads
    
- Why thread ID has nothing to do with CPU core
    
- Why a single CPU core can run many threads
    
- Context switching
    
- Why thread execution order isn't guaranteed
    
- Why output ordering can change between runs
    
- What `RUNNABLE` actually means
    
- Running vs runnable
    
- Why you cannot generally say "Thread A runs first"
    

And importantly:

> **Concurrency does not necessarily mean simultaneous execution.**

Two threads can be concurrent even on one CPU core.

---

### 4. `sleep()`

Understand:

```java
Thread.sleep(5000);
```

completely.

Including:

- Which thread sleeps?
    
- Does sleeping stop the JVM?
    
- Does sleeping stop other threads?
    
- Does it release CPU?
    
- Why does it produce `TIMED_WAITING`?
    
- Is 5000 ms an exact guarantee?
    
- What happens after the timeout?
    
- Can a sleeping thread wake earlier?
    
- What exception does `sleep()` involve?
    
- Why is `InterruptedException` checked?
    

You should be able to predict the behavior of small programs involving `sleep()`.

---

### 5. `join()` `join()` makes the thread calling `join()` wait for the target thread to finish.

This should be part of raw `Thread mastery.

Understand:

```java
thread.join();
```

Conceptually:

> "The current thread waits for this other thread to finish."

For example:

```text
main
 │
 ├── start worker
 │
 ├── join worker
 │       │
 │       └── worker runs
 │
 │       worker finishes
 │
 └── main continues
```

Learn:

- `join()`
    
- `join(milliseconds)`
    
- Which thread is waiting
    
- What state the waiting thread enters
    
- Why `join()` is different from `sleep()`
    
- `join()` and `InterruptedException`
    

---

### 6. Thread identity

Understand:

```java
Thread.currentThread()
```

and:

```java
thread.getName()
thread.getId()
thread.getState()
thread.isAlive()
```

Also learn:

```java
thread == Thread.currentThread()
```

and understand what it means.

You should be comfortable answering:

> "Which thread is executing this particular line?"

---

### 7. Thread naming

Learn:

```java
thread.setName("Worker");
thread.getName();
```

and why meaningful names are extremely useful for debugging.

For example:

```text
main
database-worker-1
http-worker-2
```

rather than:

```text
Thread-0
Thread-1
Thread-2
```

Also understand when the name is assigned and what changing it does.

---

### 8. Thread priority

Understand:

```java
thread.setPriority(...)
thread.getPriority()
```

including:

```text
MIN_PRIORITY = 1
NORM_PRIORITY = 5
MAX_PRIORITY = 10
```

But most importantly:

> **Priority is not a scheduling command.**

Don't fall into:

```text
priority 10 → definitely runs first
```

It doesn't mean that.

Understand what Java actually promises versus what the OS/JVM may do.

---

### 9. Daemon threads

This is important enough to deserve its own section.

Understand:

```java
thread.setDaemon(true);
```

and:

```java
thread.isDaemon();
```

The fundamental concept:

```text
Non-daemon threads
       ↓
keep JVM alive

Daemon threads
       ↓
do not keep JVM alive
```

Understand what happens when the last non-daemon thread terminates.

Also learn:

- When `setDaemon()` must be called
    
- What happens if you try to change daemon status after starting
    
- Why daemon threads are appropriate for some background work
    
- Why you shouldn't rely on daemon threads for critical cleanup
    

---

### 10. Exceptions inside threads

Suppose:

```java
@Override
public void run() {
    throw new RuntimeException("Boom");
}
```

Understand:

- Which thread gets the exception?
    
- Does the main thread automatically catch it?
    
- Does the whole JVM necessarily terminate?
    
- What happens to that thread?
    
- What happens if `main()` has a `try/catch` around `start()`?
    
- Uncaught exceptions
    
- `UncaughtExceptionHandler`
    

This is a surprisingly important mental-model test.

---

### 11. Thread interruption

This one deserves serious attention.

Learn:

```java
thread.interrupt();
thread.isInterrupted();
Thread.interrupted();
```

And especially understand:

> **`interrupt()` does not mean "kill this thread."**

That's a common misconception.

Understand what interruption actually means and how blocking methods such as `sleep()` react to it.

Then learn:

```java
InterruptedException
```

and the difference between:

```java
thread.isInterrupted()
```

and:

```java
Thread.interrupted()
```

This is one of the places where I would spend real time.

---

### 12. Thread lifecycle operations

Understand what happens with:

```java
start()
run()
sleep()
join()
interrupt()
```

as a combined system.

You should eventually be able to look at:

```java
Thread t = new MyThread();

t.start();
t.join();
```

and mentally explain what happens to **both threads**.

---

### 13. `Thread.State` in real programs

Don't just print:

```java
thread.getState();
```

Build tiny experiments where you deliberately produce:

```text
NEW
RUNNABLE
TIMED_WAITING
WAITING
BLOCKED
TERMINATED
```

For example, eventually:

```text
NEW
  ↓
start()
  ↓
RUNNABLE
  ↓
sleep()
  ↓
TIMED_WAITING
  ↓
wake
  ↓
RUNNABLE
  ↓
finish
  ↓
TERMINATED
```

Then we'll deliberately create `BLOCKED` and `WAITING`.

That gives you a **real** understanding rather than memorization.

---

# 14. Thread-safety boundary

There's one concept I'd introduce **before leaving raw `Thread`**, even though it technically leads into the next topic:

### Multiple threads share the same JVM memory.

For example:

```java
int counter = 0;
```

Two threads can potentially access the same object/variable.

Understand:

```text
Thread A ─────┐
              ├── shared object
Thread B ─────┘
```

But **don't yet dive deeply into synchronization**.

Just make sure you understand:

> Threads have independent execution paths, but they can operate on the same objects/memory.

That distinction sets up the entire concurrency chapter.

---

# 15. Platform threads vs virtual threads

Since you're learning modern Java, I would **absolutely** include this before considering the Thread topic complete.

Understand the distinction between:

```text
Platform Thread
```

and:

```text
Virtual Thread
```

At a conceptual level first.

You don't need to jump into virtual-thread internals yet, but you should know that modern Java has more than one threading model.

---

# The actual order I'd recommend

Don't treat the above as a random checklist. I'd go through it like this:

```text
                    RAW THREAD
                        │
                        ▼
              1. Thread object
                        │
                        ▼
              2. extends Thread
                        │
                        ▼
               3. run() / start()
                        │
                        ▼
               4. Thread lifecycle
                        │
                        ▼
               5. Thread.State
                        │
                        ▼
                6. Scheduling
                        │
                        ▼
                  7. sleep()
                        │
                        ▼
                  8. join()
                        │
                        ▼
              6. Thread identity
                        │
                        ▼
             7. Naming / priority
                        │
                        ▼
               8. Daemon threads
                        │
                        ▼
              9. Exceptions
                        │
                        ▼
             10. Interruption
                        │
                        ▼
          14. Shared memory concept
                        │
                        ▼
       15. Platform vs Virtual Threads
                        │
                        ▼
             ✅ RAW THREAD DONE
```

**Only after that** I'd move to:

```text
Runnable
   ↓
ExecutorService
   ↓
Thread pools
   ↓
Callable / Future
   ↓
shared state
   ↓
race conditions
   ↓
synchronized
   ↓
locks
   ↓
volatile
   ↓
atomic classes
   ↓
concurrent collections
   ↓
CompletableFuture
   ↓
virtual-thread programming
```

And honestly, given how you're approaching this, I'd make each section **experimental**: write a 10–20 line program, predict the output/state, run it, then explain why. That's much better for threads than reading 40 pages of API documentation.

**For where you are right now, the immediate next thing is not `Runnable`: it's `Thread` lifecycle and `Thread.State`.** We can stay there until `NEW → RUNNABLE → TIMED_WAITING → TERMINATED` is completely intuitive.


