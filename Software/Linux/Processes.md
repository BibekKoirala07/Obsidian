#### Introduction to Process and process Attributes
###### What is process
All the application programs we are running are processes.  process is simply an instance of one or more related tasks(threads) executing on the computer. It is running instance of a program. A program is like a recipe(a set of instructions). A process is like actually cooking the meal from that recipe. When we run a program(like opening a browser or running a terminal command), the computer creates a process to handle that task. Sometimes, a single program can start multiple processes at once to get job done efficiently. 

There is difference between processes, threads and programs(command).
Program is actual code or app(e.g. Firefox, Vs Code). process means running version of that program and. Threads means smaller unit of work within a process. A process can have one or more threads running different part of the task in parallel.
There are two types of process. Independent processes. Do their own thing, not affected by others. and Child/Related Processes. Spawned by another process, might depend on the parent. Example: A web browser starting multiple tabs as separate processes. 

What does processes use ? processes need resources to do their work, such as CPU(to run calculation and tasks), Memory/RAM(to store temporary data), Storage/SSD(to read/write files). and peripheral devices like printers, displays, sound cards or network interfaces. The main things is that OS(especially the kernel) manages create and killing processes, giving each process its fair share of CPU time, memory, etc. making sure no process misbehaves or crashes the whole system.

What happens when a process fails ? 
If the process is **independent**, it may crash without affecting others. If it's **related** to other processes, like a server and its worker processes, one crashing could impact the rest.

In Linux, we must use `ps`, `top` or `htop` command to know about more. It is like task manager to view running processes. 

Why You Should Care ?
Understanding processes helps you:
- Troubleshoot slow systems or crashes.
- Monitor system resource usage.
- Optimize apps and code for performance.
- Understand how multitasking works under the hood.
![[Pasted image 20250620202222.png]]
###### Process Type
Every time you run something in computer - a browser, a terminal command, or even a background task - we're working with a process. Not all process are same. They come in different **types**, depending on **how** and **why** they’re started and what they’re supposed to do.

Interactive Process
Started **directly by you**, either from the terminal or a graphical interface (like clicking an icon). They interact with you in real-time. `bash firefox top slack libreOffice`.

Batch process(Automated, pre-scheduled jobs, no need to watch them)
Run **automatically in the background** without your constant input. Usually scheduled tasks. They don’t interact with the user and follow **FIFO** order.
`updatedb ldconfig`.

Daemon Process(always running in the background)
These are **background services** that usually start **at boot time** and keep running, waiting to serve user/system requests. `httpd sshd libvirtd cupsd`.

Threads(A helper doing part of the work inside a bigger process)
**Mini-processes** within a main process. They share memory and resources, but can be scheduled and managed independently by the OS. Many modern apps use threads.
`dconf-service gnome-terminal-server`.

Kernel Threads(Hidden low-level system management helpers)
**Low-level system tasks** handled by the kernel itself. Users can’t start, stop, or interact with these directly. They help the OS run efficiently. `kthreadd migration ksoftirqd`

Terminal Windows is itself a process, From there we can launch interactive process like text editor or system command.
If you add `&` at the end of a command (e.g., `python myscript.py &`), you’re sending that process to the **background**, so it keeps running even if you close the terminal.
###### Process Scheduling and States
![[Pasted image 20250620204355.png]]
Our computer's brain(CPU) can only focus on one thing at a time per core. So how it manage to run many apps and tasks at once ? 
This is where the scheduler comes in - a smart part of the OS kernel that decides which process run next, How long it runs, When to pause it and let another one take over. This creates the illusion that everything is happening at once — even though the CPU is rapidly switching between processes.

How does scheduling work ? 
The scheduler shares CPU time based on: 
- Priority (important tasks get more time)
- How long a process has run already,
- How much more time it still needs.
Each process gets a short time slice (a tiny bit of CPU time). If it's not done, it goes back in line and waits for its next turn.

A process isn’t always “running” — it moves between different **states**. Here are the common ones:

| State    | What it means                                                                                       | Where it waits    |
| -------- | --------------------------------------------------------------------------------------------------- | ----------------- |
| Running  | Actively using the CPU or waiting for its turn to get CPU time.                                     | On the run queue  |
| Sleeping | Waiting for something (e.g. user input, a file to load). Not ready to run yet.                      | On the wait queue |
| Stopped  | Paused by the user or the system.                                                                   | Not active        |
| Zombie   | The process has finished, but its parent hasn't cleaned it up yet. Still listed in system tables.\| | In limbo          |
Run Queues and Wait Queues
- **Run Queue**: A list of processes ready to run or waiting for CPU time. Each CPU/core has its own queue.
- **Wait Queue**: A list of sleeping processes waiting for something external (input/output, etc.) to happen before they can run again.

Zombie Process
A zombie is a process that has finished, but its parent process hasn’t acknowledged it yet (hasn't called `wait()` to collect its exit info). So it's technically "dead" — but still visible in the system process list. It's harmless in small numbers, but too many zombies can clog up the system. Zombie process may be necessary to Understand what the kernel is doing behind the scenes, Spot issues like too many sleeping or zombie processes.
###### Process and Thread Ids 
The computer run many process and threads at same time. To keep track of them, the operating system gives each one a unique number.


| Id Type                 | What it does                                                                                |
| ----------------------- | ------------------------------------------------------------------------------------------- |
| PID(Process ID)         | A unique number given to **each running process**. Think of it as a name tag.               |
| PPID(Parent Process ID) | The PID of the process that started this one. Every process has a parent\|                  |
| TID(Thread ID)          | A unique number for a **thread** inside a process. For single-threaded programs, TID = PID. |
Since OS needs to Monitor how much CPU and memory each process/thread uses, track what it's doing and where it data is stored, keep things organized so it can pause, resume or kill processes if needed. So OS uses there IDs. Without these, the OS would be totally lost. 

Relationships between processes
Processes don't live alone - they often start other processes, becoming their parent. 
For example, when you open your terminal and type `nano notes.txt`, your terminal process is the **parent**, and `nano` is the **child**. The child process gets a **new PID**, and its **PPID** will be the parent’s PID.

Orphaned and Adopted Processes
Sometimes, a **parent process dies** before its child. In that case:
- The system assigns a new "adoptive parent" to the orphaned process.
- On modern Linux systems, the adoptive parent is usually a **kernel thread** called `kthreadd`, which has **PPID = 2**.
This keeps the system stable and avoids memory leaks.

Single vs Multi-threaded Programs
Single threaded has only one flow of execution and PID = TID. 
Multi-threaded program: has multiple threads doing different tasks within one process. All threads share the same PID. But each thread gets its own unique TID

Why It’s Useful to Know
Understanding these IDs helps you:
- **Debug** programs or kill specific tasks using tools like `ps`, `top`, `htop`, or `kill`.
- Track **which process is using too much memory or CPU**.
- Understand how processes **spawn and organize** themselves.
###### Terminating a process(a.k.a "Killing a process")
Sometimes, a program gets stuck, freezes, or uses too many resources (like RAM or CPU).

How do you kill a process ? 
We can use `kill` command in the terminal. 
```
	kill -9 <PID> // kill -SIGKILL <PID>
```
`-9` or `-SIGKILL` tell the system to immediately stop the process, no question asked And we can only kill processes we own(process we started). To kill other users' processes, we need to be root or use `sudo`.

==Important==
The `kill` command doesn’t always “kill.” It actually sends a **signal** to the process. There are many types of signals, not just for termination.

| Command          | Signal Name | What it does                                             |
| ---------------- | ----------- | -------------------------------------------------------- |
| `kill -15 <PID>` | `SIGTERM`   | Polite_ way to ask a process to stop (can be ignored).\| |
| `kill -9 <PID>`  | `SIGKILL`   | _Force stop_. Process is killed immediately.             |
| `kill -1 <PID>`  | `SIGHUP`    | Often used to reload configuration                       |
We can see full list of signals by running
```
	kill -l
```
Before we can kill something, we need to know its PID. We can find it using
```
	ps aux | grep <process-name>
	top or htop
```
eg. `ps aux | grep firefox`.
Use `-9` only when 
- The process is not responding to the regular `kill` (which uses `SIGTERM` by default),
- You need to forcefully shut it down.
Because `SIGKILL` cannot be ignored, the process is immediately terminated, even if it’s doing important things like saving data — so be cautious. Always gentle(try `kill -15`) before going full Terminator mode `9`.
###### User and Group Ids
We know Linux and Unix are multi-user systems, many users can use the same system at once - each doing different things like running programs, accessing files, etc.
To keep track of who’s doing what, the system uses User IDs (UIDs) and Group IDs (GIDs). Think of it like this:
- **UID** = your personal ID card.
- **GID** = the club or team you belong to.
![[Pasted image 20250620213032.png]]
Types of Ids Explained

| Id Type | What is represents                                                                     |
| ------- | -------------------------------------------------------------------------------------- |
| UUID    | Your unique user ID. It’s what the system uses to identify you.                        |
| GID     | The ID of your primary group (like a team or permission group).                        |
| RUID    | **Real User ID** — the person who actually started the process.                        |
| EUID    | **Effective User ID** — used to **check what permissions** the process should have.    |
| RGID    | **Real Group ID** — your original group when the process started                       |
| EGID    | **Effective Group ID** — used to check **group-level permissions** during the process. |
Need of Real vs Effective Ids
Let’s say you open a **system tool** that needs **admin permissions**. Even though _you_ started the process, the tool runs with **higher privileges**.
- Your **RUID** stays the same (you’re still you),
- But the process runs with a different **EUID** (e.g., `root`) — so it can access more things.
Think of it like wearing a **VIP badge** temporarily: you’re still the same person underneath, but now you can enter restricted areas.

Groups and Group IDs
Users can belong to **one or more groups**. Groups are used to **organize users and share access** to files, folders, and devices. Each user has:
- A **primary group** (with a **GID**),
- Optional **secondary groups**.
Group permissions determine **what users can do as a team** — like edit shared files or use shared printers.
```bash
id      # shows UID, GID, and groups
whoami  # shows your username
groups  # shows all groups you belong to

# example output
uid=1001(bibek) gid=1001(bibek) groups=1001(bibek),27(sudo)
```
###### More about Priorities
Your computer is always juggling lots of processes. But each CPU core can only run one process at a time. So, how does it decide _which_ one runs next? That’s where process priorities come in. Just like in real life, some tasks are *ore urgent. In Linux, you can assign a priority to a process using a value called "niceness".

So what is Niceness ? (a.k.a nice value)
**Niceness** controls how "polite" a process is about using the CPU. A **nicer process waits its turn**, letting others go first. A **less nice (lower nice value) process fights harder** to get CPU time.
IMPORTANT: Lower nice value = **higher priority**  
Higher nice value = **lower priority**

| Nice Value | Priority | Behaviour                              |
| ---------- | -------- | -------------------------------------- |
| -20        | Highest  | Very demanding, takes CPU aggressively |
| 0          | Default  | Normal process                         |
| +19        | Lowest   | Very nice, yields to others easily     |

So yes — **more “nice” means less priority**, which sounds backward, but it's just old-school UNIX humor.

We can start a process with nice value
```
	nice -n 10 command_name
```
We can change the priority of a running process(need an PID).
You need **root privileges** to assign _negative_ nice values (higher priority).

What about Real-time Priorities ? 
Some tasks are **super time-sensitive** — like reading sensor data, controlling a robot arm, or handling live video feeds. Linux allows **real-time scheduling policies** for these cases. These processes are **even higher in priority** than nice values can offer.
Real-time priorities come in two main flavors:
- **SCHED_RR** (Round Robin): shares time fairly among real-time tasks.
- **SCHED_FIFO** (First In, First Out): runs until it finishes or gets interrupted.
But be careful: real-time tasks can **hog the CPU** and starve other processes if not managed properly.

Note on Hard Real-Time
- Real-time in Linux is **soft real-time** — meaning it _tries_ to meet timing goals.
- **Hard real-time** means it _must_ meet them, no excuses. That’s used in systems like aircraft or pacemakers — and requires special real-time operating systems (RTOS).
- For critical systems, Linux offers **real-time priorities**, but use with caution!
#### Process metrics and Process Control
###### Load Average
The **load average** is a way to measure **how busy your system’s CPU(s)** are over time. It gives you a quick snapshot of how much **work** your system is trying to do — and whether it’s keeping up. It shows **three numbers** that represent the system load over:
- **1 minute**
- **5 minutes**
- **15 minutes**
```bash
$ uptime
 15:00:01 up 2 days,  3:01,  2 users,  load average: 0.35, 0.45, 0.60
```
This means:
- In the last 1 minute, the average load was 0.35
- In the last 5 minutes, it was 0.45    
- In the last 15 minutes, it was 0.60
But what do these numbers _mean_?

What does "Load" include ? 
In Linux, load average includes:
1. Processes currently using the CPU (actively running)
2. Processes waiting their turn on the run queue (runnable but not yet running)
3. Sleeping processes — specifically uninterruptible sleepers (waiting for resources like disk or network I/O)
> This is **different from other UNIX systems**, where sleeping processes usually aren't counted. Linux includes them, but **only** the ones that are “uninterruptible” (can’t easily be stopped or delayed).

How do you interpret Load Averages ? 
To understand if your system is overloaded, compare the load average to the **number of CPU cores**.

Example:
You have **2 CPU cores**.
- Load average: `1.00` = all cores are fully utilized.
- Load average: `2.00` = both cores are busy, but not overloaded.
- Load average: `3.00+` = more work than your system can handle — tasks are queuing up.

For **N cores**, a load average below **N** means you're OK. Above **N** = overloaded.

To view load average, we can check it with these commands
```bash
	uptime # quick summary of system load
	top    # liver interactive view of processes + load
	w      # shows who's logged in + system load
```

Load average tell us how many processes are actively competing for the CPU - including _uninterruptible sleepers_ in Linux. It helps you **monitor CPU pressure** over 1, 5, and 15-minute windows. Compare it to your **CPU core count** to know if your system is overloaded.
###### Interpreting Load Averages
When we run command like `top` and `uptime`, we see 
```bash
	load average: 0.45, 0.17, 0.12
```
These three numbers show the system load over:
- 1 minute
- 5 minutes
- 15 minutes
What do these numbers means ? 

| Time window | Load Avg | What it means                              |
| ----------- | -------- | ------------------------------------------ |
| 1 min       | 0.45     | CPU was 45% busy in last minutes           |
| 5 min       | 0.17     | CPU was 17% busy on average over 5 minutes |
| 15 min      | 0.12     | CPU was 12% busy over the last 15 minutes  |
So overall: The system is **under very light load** — it’s mostly idle

What if Load is 1.00 or higher ? 
On a **single-CPU system**:
- `1.00` = CPU fully used (100% busy)
- `>1.00` = Overloaded! More tasks are waiting than CPU can handle
On a **multi-core system**, you divide by the number of CPUs.
Example: 
If your system has **4 CPUs**:
- A 1-minute load average of **4.00** means full usage (100% utilized)
- A value like **5.00** = overloaded (some tasks had to wait)
- A value like **2.00** = only 50% of the system was busy

When should you worry ? 
- **Short spikes** are normal — like during startup or heavy app launches.
- If **1-minute value is high**, but **5 and 15-minute values are low**: it’s probably just a short burst. Not a big deal.
- If **5 and 15-minute values stay high**, that’s a warning sign.
    - Your system might be **consistently overloaded**.
    - It could slow down, or some tasks may be delayed.

###### Background and Foreground Processes
When you run a command in terminal, it becomes a job - and jobs can run either in the foreground or background.

Foreground process
- Runs **directly in the terminal**.
- You **can’t use the terminal** for anything else until it finishes.
- Good for quick tasks (like `ls`, `cd`, `mkdir`, etc.)
Background process
- Runs **in the background**, freeing up your terminal.
- You can **keep using the shell** while the process does its work.
- Ideal for **long-running tasks** like backups, indexing, scripts, etc.

How to run in the backgroun
Just add `&` at the end of your command
```bash
	updatedb &
```
This:
- Starts `updatedb` in the **background**,
- Immediately gives you your terminal back,
- Runs the task at a **lower priority** so it doesn’t affect interactive commands.

Managing Jobs
The things is we can move jobs between foreground and background easily:

| Action                   | Command or Shortcut | What it Does                                           |
| ------------------------ | ------------------- | ------------------------------------------------------ |
| Run in background        | `command &`         | Starts a job in the background                         |
| Suspend foreground job   | `Ctrl + Z`          | Pauses the job and puts it in the background (stopped) |
| Move suspended job to bg | `bg`                | Resumes the paused job in background                   |
| Move job to fg           | `fg`                | Brings a background job to foreground                  |
| Kill foreground job      | `Ctrl + C`          | Stops the job completely                               |
| List background jobs     | `jobs`              | Shows jobs running or stopped in the background        |
###### Managing Jobs
Once you've started some **background jobs**, you’ll want to keep track of them — see what’s running, bring them back to the foreground, or even stop them. Linux gives you a simple tool for this: the `jobs` command.

The `jobs` utility shows a list of **all background and suspended jobs** **started from your current terminal**.
```bash
	jobs
```
You'll see output like
```
[1]+  Running                 updatedb &
[2]-  Stopped                nano
```
`[1] [2]` = Job ID(not the same as PID). `Running`, `Stopped` = Current State.  We can see PID also. 

==Important==
Jobs Are Tied to the Terminal.
- **Jobs only live inside your current terminal session.**
- If you **log out** or **close the terminal**, any background jobs you started **will also go away**.
- That means `jobs` will not show background tasks started from **other terminals** or from **closed sessions**.
#### Listing Process: `ps` and `top`
`ps` means process status. just a command line tool that shows you **what’s currently running** on your Linux system — one snapshot at a time. It’s like taking a photo of all the active processes: what’s running, who started it, how much CPU or memory it’s using, and more. For **live**, real-time updates (like a video instead of a photo), use `top`, `htop`, `atop`, or `btop`.

Just type `ps` and this shows only the process running in your current shell (i.e., only what you started from this terminal).

For more details just use
```bash
	ps -ef
```
- Shows **all processes** on the system.
- Includes **every user**, not just your own.
- Commonly used when tracking down rogue or misbehaving processes.
or 
```bash
	ps -u bibek
```
Lists **only** the processes running under the specified username.
or 
```bash
	ps -eLF
```
- Shows **each thread** as its own line.
- Helpful when debugging multi-threaded apps.
or 
```bash
	ps aux
```
- `a` → Show processes from **all users**
- `u` → Display in **user-oriented format** (shows owner, CPU, memory, etc.)
- `x` → Include **processes not attached to a terminal** (like daemons)
This is a **classic go-to command** for inspecting the system.
or 
BSD Style skips the dash and gives you more control and flexibility. 
```bash
	ps aux # BSD Style
	ps -ef # system V
```
###### Process tree
```bash
	pstree
```
When you're dealing with dozens (or hundreds) of processes, it can be hard to understand **how they relate** to each other. That’s where the `pstree` command comes in. Instead of showing processes as a flat list like `ps`, `pstree` shows them in a **tree-like hierarchy**, which makes it easier to see:
- **Parent-child relationships**
- **Which process started what**
- **What’s running inside what**

What does `pstree` show ? 
- The **root process** at the top (usually `systemd` or `init`)(parent of almost everything)
- All child processes branching from their **parent processes**
- **Threads** shown/appear in `{curly braces}`
- No repeated processes — each one appears once in the tree.
###### top
Rather than `ps`(just a snapshot), `top` is real-time system monitor that shows 
- Which processes are running
- How much CPU and memory each is using
- Overall system health (load average, uptime, usage)
It updates **every 2 seconds by default**, and you can **interact with it** while it’s running.

With top we see 
- **Load average**: How busy the CPU has been (1, 5, 15 mins)
- **Tasks**: Number of total, running, and sleeping processes
- **CPU usage**: Breakdown of CPU time — user, system, idle, etc.
- **Memory**: Total RAM, used, free, and cached memory

The second line of the **top** output displays the total number of processes, the number of running, sleeping, stopped, and zombie processes. Comparing the number of running processes with the load average helps determine if the system has reached its capacity or perhaps a particular user is running too many processes. The stopped processes should be examined to see if everything is running correctly.
###### Third Line of the top Output
###### Fourth and Fifth Lines of the top Output
###### Process List of the top Output
###### Interactive keys with top

#### Starting Processes in the future
###### Scheduling Future Processes using `at`
The `at` command lets you **schedule a one-time task** to run at a **specific future date and time**. Perfect for:
- Running a backup while you’re out
- Sending logs or emails automatically
- Scheduling a long-running job when the system is idle
There is queue for scheduled job. Obviously we can remove a scheduled job. 
Important
1. The command **must be non-interactive** (i.e., doesn’t expect user input).
2. You **must have the `atd` service running** on your system.

==Important==
Use `cron` instead of `at`
- `at` = **run once**
- `cron` = **run on a schedule (daily, weekly, etc.)** ==
###### cron
**`cron`** is a **time-based job scheduler** in Linux.  
It runs **commands or scripts automatically** at scheduled times — daily, weekly, hourly, or even down to the minute.
If you’ve ever thought:

> “I want this script to run every night at 1 AM” —  
> **cron** is the tool for the job.

`cron` reads its jobs from a **crontab** (cron table) file. There are two types:
1. **System-wide crontab**: `/etc/crontab`
2. **User-specific crontabs**: Managed by individual users.

###### anacron
**`anacron`** is a smarter, more **power-aware** version of `cron` — designed for systems that **don’t run 24/7** (like personal laptops, desktops, or servers that get shut down).
The Problem with `cron`:
- `cron` assumes your system is **always on**.
- If a job is scheduled for **3 AM**, and your system is off — that job just **won’t run**.
Bad news for things like backups or maintenance tasks.

**`anacron` guarantees** that daily, weekly, and monthly jobs **will run** — even if your system was off when they were originally scheduled. When your system boots, `anacron` **catches up** on any missed jobs.
###### sleep
The `sleep` command simply **pauses** (or suspends) a script or command for a specific amount of time.
Imagine telling your computer:
> “Wait a bit... then continue.”
It’s like pressing **"pause"** in the middle of a sequence.

When Would You Use It ?
- To **delay a command** so a device, server, or resource has time to become ready.
- To **space out commands** in a loop (e.g., checking something every few seconds).
- To **avoid overloading** a system by staggering processes.
- In automation: “Do task A, wait, then do task B.”

Delay is relative means "wait X time(second, minutes, ...)"