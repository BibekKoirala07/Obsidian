Here are 4 hands-on tasks designed to test every stage of a thread's lifetime (`NEW`, `RUNNABLE`, `TIMED_WAITING`, and `TERMINATED`) using only `Thread` subclassing.

Try building them one by one in your editor.

### Task 1: The Identity & Lifetime Inspector

**Goal:** Observe a thread move through different lifecycle states (`NEW`, `RUNNABLE`, `TERMINATED`) using `getState()` and `isAlive()`.

  

1. Create a class `InspectorThread` that extends `Thread`.
    
      
    
2. Inside its `run()` method, loop 3 times printing `"Working..."`.
    
      
    
3. In your `main` method:
    
      
    - Instantiate `InspectorThread`. Print its state (`getState()`) and whether it is alive (`isAlive()`).
        
          
        
    - Call `.start()`. Print its state and `isAlive()` immediately after starting.
        
          
        
    - Put `main` to sleep for 100ms using `Thread.sleep(100)` so `InspectorThread` has time to finish.
        
          
        
    - Print its state and `isAlive()` one final time.
        
          
        

**What you should notice:**

  

- Before `start()`, state is `NEW` and `isAlive()` is `false`.
    
      
    
- After `start()`, state is `RUNNABLE` and `isAlive()` is `true`.
    
      
    
- After it finishes execution, state is `TERMINATED` and `isAlive()` is `false`.
    
      
    

### Task 2: The Illegal Restart Attempt

**Goal:** Prove to yourself that a thread's lifecycle is strictly one-way and cannot be restarted.

  

1. Create a class `SingleUseThread` extending `Thread` whose `run()` method simply prints `"Running single task..."`.
    
      
    
2. In `main`:
    
      
    - Instantiate the thread and call `.start()`.
        
          
        
    - Wait 100ms using `Thread.sleep(100)` to ensure it completes and reaches `TERMINATED`.
        
          
        
    - Call `.start()` on the **same object** a second time.
        
          
        

**What you should observe:**

  

- Java will throw an `IllegalThreadStateException`. A dead thread cannot be revived.
    
      
    

### Task 3: State Capture (`TIMED_WAITING`)

**Goal:** Catch a thread while it is paused inside `Thread.sleep()`.

  

1. Create a class `SleepingThread` extending `Thread`.
    
      
    
2. Inside its `run()` method, make it sleep for 2 seconds (`Thread.sleep(2000)`).
    
      
    
3. In `main`:
    
      
    - Instantiate and start `SleepingThread`.
        
          
        
    - Put `main` to sleep for 500ms using `Thread.sleep(500)` so the child thread enters its sleep state.
        
          
        
    - From `main`, print `sleepingThread.getState()`.
        
          
        

**What you should observe:**

  

- The state printed will be **`TIMED_WAITING`**, showing that the thread is paused waiting for a timer to elapse.
    
      
    

### Task 4: Coordination with `join()`

**Goal:** Control thread termination order so `main` waits for worker threads to complete before finishing.

  

1. Create a class `DownloaderThread` extending `Thread` that accepts a filename string in its constructor.
    
      
    
2. Inside `run()`, print `"Downloading [filename]..."`, sleep for 1000ms, and then print `"Finished [filename]"`.
    
      
    
3. In `main`:
    
      
    - Create two threads: `DownloaderThread file1` and `DownloaderThread file2`.
        
          
        
    - Start both threads.
        
          
        
    - Use `.join()` on both threads (`file1.join(); file2.join();`) to force `main` to pause until both finish.
        
          
        
    - Print `"All downloads complete! Main exiting."` as the last line of `main`.
        
          
        

**What you should observe:**

  

- `"All downloads complete! Main exiting."` will **always** print last, regardless of CPU execution speed.


First master `Thread` itself: lifecycle, `start()`, `run()`, scheduling, sleep, join, interruption, states, daemon threads, synchronization basics, etc.

# Thread Mastery Challenge Roadmap

## Level 1 — Fundamental Understanding

### Task 1: `start()` vs `run()`

Create two `MyThread` objects.

Experiment with:

```java
thread1.run();
thread2.run();
```

Then:

```java
thread1.start();
thread2.start();
```

### Your mission:

Print:

```java
System.out.println(Thread.currentThread().getName());
```

from inside `run()`.

Answer these questions:

1. Why does `run()` execute on `main` when called directly?
    
2. Why does `start()` execute on another thread?
    
3. Does `start()` immediately guarantee execution?
    
4. Who decides when the new thread actually gets CPU time?
    

---

# Level 2 — Thread Naming and Identity

### Task 2: Create 5 Named Threads

Create:

- Downloader-1
    
- Downloader-2
    
- Downloader-3
    
- Downloader-4
    
- Downloader-5
    

Each thread should print:

```text
Downloader-1 started
Downloader-1 running on: Downloader-1
Downloader-1 finished
```

Use:

```java
getName()
Thread.currentThread().getName()
getId()
```

### Requirement

Inside `run()`, prove the difference between:

```java
this.getName()
```

and:

```java
Thread.currentThread().getName()
```

Then explain when they could potentially be different.

---

# Level 3 — Understanding Scheduling

### Task 3: The Race

Create two threads:

```text
Thread-A
Thread-B
```

Each prints numbers from 1 to 20.

Example:

```text
Thread-A: 1
Thread-B: 1
Thread-B: 2
Thread-A: 2
Thread-A: 3
...
```

Run the program multiple times.

### Your mission:

Explain why the output changes.

You are NOT allowed to assume:

> "Thread-A started first, so it should finish first."

You need to understand that:

```java
threadA.start();
threadB.start();
```

does not determine exact execution order.

---

# Level 4 — `sleep()` Mastery

### Task 4: Traffic Light Simulation

Create three threads:

- Red Light
    
- Yellow Light
    
- Green Light
    

Each should:

```text
Red Light ON
(wait)
Red Light OFF
```

Use different sleep durations.

Example:

```java
Thread.sleep(2000);
```

### Questions you must answer:

1. What happens to the thread during `sleep()`?
    
2. Does sleeping release CPU?
    
3. Does sleeping release a synchronized lock?  
    (Important question—answer later when you learn synchronization.)
    
4. Why must `sleep()` handle `InterruptedException`?
    
5. Does `Thread.sleep()` belong to a specific thread object?
    

Understand this deeply:

```java
Thread.sleep(1000);
```

is static.

It always affects the **currently executing thread**.

---

# Level 5 — `join()` Mastery

This is extremely important.

### Task 5: Sequential Dependency

Create three threads:

- DownloadThread
    
- ProcessThread
    
- UploadThread
    

Each prints messages and sleeps.

You must enforce:

```text
Download
↓
Process
↓
Upload
```

Even though all are separate threads.

Use:

```java
thread.join();
```

### Example expected output:

```text
Download started
Download finished

Processing started
Processing finished

Upload started
Upload finished
```

### Challenge

Without `join()`:

```java
download.start();
process.start();
upload.start();
```

What happens?

With `join()`:

```java
download.start();
download.join();

process.start();
process.join();

upload.start();
upload.join();
```

What exactly is the `main` thread doing while waiting?

---

# Level 6 — Thread States Investigation

This is where things get interesting.

### Task 6: Observe Thread Lifecycle

Create a thread that sleeps for 5 seconds.

From `main`, inspect its state:

```java
System.out.println(thread.getState());
```

Print the state:

1. Before `start()`
    
2. Immediately after `start()`
    
3. While sleeping
    
4. After completion
    

You should investigate states like:

```text
NEW
RUNNABLE
TIMED_WAITING
TERMINATED
```

### Goal

Produce something similar:

```text
Before start: NEW
After start: RUNNABLE
While sleeping: TIMED_WAITING
After finish: TERMINATED
```

Important: timing may make results slightly unpredictable.

Your job is to understand why.

---

# Level 7 — Multiple `start()` Problem

### Task 7: Can a Thread Be Restarted?

Try this:

```java
MyThread thread = new MyThread("Worker");

thread.start();
thread.join();

thread.start();
```

What happens?

Investigate the exception.

Then answer:

> Why can a Java Thread object only be started once?

This is an important lifecycle concept.

---

# Level 8 — Interruption Mastery

Your current understanding has one thing that needs correction.

You wrote:

```java
Thread.currentThread().interrupt(); // it tells java to stop this thread
```

⚠️ Not exactly.

`interrupt()` does **NOT forcefully stop a thread**.

It sends an **interruption request/signal**.

You need to master this.

---

## Task 8: Interrupt a Sleeping Thread

Create:

```java
class Worker extends Thread {
    @Override
    public void run() {
        try {
            Thread.sleep(10000);
        } catch (InterruptedException e) {
            System.out.println("I was interrupted!");
        }
    }
}
```

From main:

```java
worker.start();

Thread.sleep(2000);

worker.interrupt();
```

Expected behavior:

```text
Worker started
I was interrupted!
```

### Questions:

1. Why does `sleep()` throw `InterruptedException`?
    
2. What happens to the interrupt flag when `InterruptedException` is thrown?
    
3. Why do developers often write:
    

```java
Thread.currentThread().interrupt();
```

inside the catch block?

This is a very important concept.

---

# Level 9 — Cooperative Cancellation

### Task 9: Long Running Worker

Create a thread:

```java
class Worker extends Thread
```

It should continuously work:

```text
Working...
Working...
Working...
Working...
```

Your job is to stop it gracefully using:

```java
interrupt();
```

You should check:

```java
isInterrupted()
```

Example concept:

```java
while (!Thread.currentThread().isInterrupted()) {
    System.out.println("Working...");
}
```

Then from main:

```java
worker.start();

Thread.sleep(3000);

worker.interrupt();
```

The worker should exit gracefully.

### Goal

Understand this:

> Threads should cooperate with interruption.

Java doesn't normally kill your thread instantly.

---

# Level 10 — Daemon Threads

### Task 10: Background Service

Create two threads:

### User Thread

```text
Main Worker
```

### Daemon Thread

```text
Background Cleaner
```

Set:

```java
daemonThread.setDaemon(true);
```

The daemon should continuously print:

```text
Cleaning...
```

The normal thread should finish after 5 seconds.

Observe what happens to the JVM.

### Questions:

1. Why does the daemon thread automatically stop?
    
2. What happens when all user threads finish?
    
3. Can you call:
    

```java
setDaemon(true);
```

after `start()`?

Try it.

---

# Level 11 — Thread Priority Experiment

### Task 11: Priority Test

Create three threads:

```text
LOW_PRIORITY
NORMAL_PRIORITY
HIGH_PRIORITY
```

Set:

```java
thread.setPriority(Thread.MIN_PRIORITY);
thread.setPriority(Thread.NORM_PRIORITY);
thread.setPriority(Thread.MAX_PRIORITY);
```

Each thread performs heavy computation.

Run the experiment multiple times.

### Your mission:

Determine whether high-priority always finishes first.

Correct answer:

> Probably not guaranteed.

You need to understand that priority is generally a scheduling hint, not a guarantee.

---

# Level 12 — Thread Lifecycle Challenge

## Task 12: Build a Thread Monitor

Create a program that monitors another thread.

### Worker:

```text
STARTED
WORKING
SLEEPING
WORKING
FINISHED
```

### Monitor Thread/Main Thread:

Continuously prints:

```text
Worker state: RUNNABLE
Worker state: TIMED_WAITING
Worker state: RUNNABLE
Worker state: TERMINATED
```

Use:

```java
getState()
isAlive()
getName()
getId()
```

The monitor must stop automatically when:

```java
worker.isAlive() == false
```

---

# 🔥 Final Boss Challenge

If you complete this properly, you're ready for `Runnable`.

## Task 13: Multi-Thread Job System

Create three custom classes extending `Thread`.

### Class 1

```java
class DownloadThread extends Thread
```

### Class 2

```java
class ProcessingThread extends Thread
```

### Class 3

```java
class BackupThread extends Thread
```

---

## Requirements

### DownloadThread

- Runs for approximately 5 seconds.
    
- Prints progress from 0% to 100%.
    
- Can be interrupted.
    
- Handles interruption correctly.
    
- Exits gracefully.
    

Example:

```text
Download: 0%
Download: 20%
Download: 40%
```

---

### ProcessingThread

- Must start only after download finishes.
    
- Use `join()`.
    
- Processes 10 items.
    
- Sleeps between each item.
    

Example:

```text
Processing item 1
Processing item 2
...
```

---

### BackupThread

Runs independently.

- Is a daemon thread.
    
- Prints:
    

```text
Creating backup...
```

every second.

---

### Main Thread

Must:

1. Start DownloadThread.
    
2. Start BackupThread.
    
3. Wait 2 seconds.
    
4. Interrupt DownloadThread.
    
5. Wait for DownloadThread using `join()`.
    
6. Start ProcessingThread only after download terminates.
    
7. Wait for ProcessingThread.
    
8. Print:
    

```text
Application finished successfully
```

---

# Graduation Test 🎓

Before I tell you to move to `Runnable`, you should be able to answer these without memorizing:

### Thread Basics

- What is the difference between a process and a thread?
    
- What is the difference between heap and stack?
    
- Does every thread have its own stack?
    
- Do threads in the same process share heap memory?
    

### Thread Creation

- What happens when you call `start()`?
    
- What happens when you call `run()` directly?
    
- Why can't you call `start()` twice?
    

### Scheduling

- Who decides which thread executes?
    
- Is thread execution order guaranteed?
    
- Does priority guarantee execution order?
    

### Lifecycle

Explain:

```text
NEW
RUNNABLE
BLOCKED
WAITING
TIMED_WAITING
TERMINATED
```

### Waiting

- What does `sleep()` do?
    
- What does `join()` do?
    
- Which thread actually waits when calling `thread.join()`?
    

### Interruption

- Does `interrupt()` kill a thread?
    
- What is the interrupt flag?
    
- Why restore interruption with:
    

```java
Thread.currentThread().interrupt();
```

### Daemon Threads

- What is the difference between daemon and user threads?
    
- When does the JVM terminate?
    

---

# My Recommended Order for You

Don't do everything randomly.

```text
1. start() vs run()
        ↓
2. Multiple threads + scheduling
        ↓
3. sleep()
        ↓
4. Thread states
        ↓
5. join()
        ↓
6. interrupt()
        ↓
7. daemon threads
        ↓
8. priority
        ↓
9. Final Boss Challenge
        ↓
10. Runnable
```

## My suggestion

Start with **Task 1 through Task 4**, send me your code and your explanations. I'll review them strictly and correct misconceptions.

Once you've completed all the important concepts—not just copied working code—I can confidently tell you:

> **"Yes. You have mastered extending `Thread`. Now move to `Runnable`."**

And yes, your instinct is correct: **master `Thread` first, then `Runnable`.**