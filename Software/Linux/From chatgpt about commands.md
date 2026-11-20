You're a backend engineer, so I'll explain this from a **systems engineering perspective**, rather than teaching you Linux commands.

The important thing is to understand what your computer is actually doing when you type a command into an Ubuntu terminal.

For example, when you type:

```
cd /home/ubuntu/projects
```

or:

```
node server.js
```

or:

```
ls -la
```

What receives that text? Who decides what the command means? Does the terminal execute it? Does Linux execute it? What is the shell responsible for? How does the operating system find the executable? How does a process start? Where does the output come from?

These are different operations involving different components.

We'll follow a command through its entire lifecycle, including the shell, Linux kernel, processes, memory, file descriptors, environment variables, pipes, and the filesystem.

# **1. First, understand what you're interacting with**

Most people think they're typing commands directly into Linux. They're not.

When you open a terminal in Ubuntu, several components work together.

**You**

Typing on your keyboard

**Terminal emulator**

GNOME Terminal, Ptyxis, Konsole, etc.

**Shell**

Usually Bash on Ubuntu

**Programs and system calls**

Command execution and requests for OS services

**Linux kernel**

Processes, memory, filesystem, scheduling, I/O

**Hardware**

CPU, RAM, storage, network interfaces

Conceptual flow. Programs and the shell both interact with the kernel; the layers aren't a strict one-way chain.

Let's understand the roles separately.

## **Terminal emulator**

The terminal is the graphical application that displays a text interface.

Examples include GNOME Terminal, Ptyxis, and Konsole.

Its main responsibilities are to:

- Accept keyboard input and pass it to the terminal session.
    
- Display the text produced by programs.
    
- Handle terminal-related features such as colors, scrolling, copy/paste, and window resizing.
    

**The terminal emulator does not normally interpret or execute** `cd`**,** `ls`**, or** `node`**.**

It's mostly an interface between you and a program running inside the terminal session.

In a typical graphical Ubuntu setup, the terminal creates a _pseudo-terminal_ (PTY). Think of this as a communication channel with two ends: the terminal emulator uses one end, while the shell and programs interact with the other.

## **Shell**

The shell is an actual program running on Linux.

On Ubuntu, the default interactive shell for many users is Bash.

You can check yours:

```
echo $SHELL
ps -p $$ -o pid,comm,args
```

`$SHELL` usually indicates your configured login shell, while `ps` helps inspect the currently running shell process.

The shell is responsible for understanding command syntax and organizing command execution.

For example, when you enter:

```
ls -la /var/log
```

Bash identifies the command name `ls` and its arguments, resolves what `ls` refers to, and arranges its execution.

For an external program, the shell typically creates a child process and starts the executable.

For a shell builtin such as `cd`, the shell handles the operation itself.

This difference is extremely important.

## **Linux kernel**

The kernel is the core of the operating system.

It manages the resources that running programs need, including:

- Processes and CPU scheduling.
    
- Virtual memory.
    
- Files and directories.
    
- File permissions.
    
- Network sockets.
    
- Devices and I/O.
    

A shell cannot directly read arbitrary disk hardware or allocate its own physical memory however it wants.

Instead, it asks the kernel to perform operations through an interface called **system calls (syscalls)**.

Examples:

|System call|Purpose|
|---|---|
|`fork()` / `clone()`|Create a process or execution context|
|`execve()`|Replace a process's program image with a new executable|
|`wait4()`|Wait for a child process|
|`openat()`|Open a file|
|`read()`|Read from a file descriptor|
|`write()`|Write to a file descriptor|
|`chdir()`|Change the current working directory|
|`pipe2()`|Create a pipe|
|`dup2()`|Duplicate or redirect a file descriptor|

Notice that there is no syscall called `ls` or `cd`.

Those are user-facing commands. The actual operating-system operations occur underneath them.

# **2. What happens when you type** `ls -la` **and press Enter?**

Let's use this command:

```
ls -la
```

Assume you are in `/home/ubuntu/projects`.

Here is the execution sequence at a high level.

1. **Keyboard input reaches the terminal session.**
    
    The terminal passes your input through the PTY to the shell, subject to terminal input handling.
    
2. **Bash reads and parses the command.**
    
    It recognizes `ls` as the command name and `-la` as an argument.
    
3. **Bash resolves** `ls`**.**
    
    It typically searches directories in `$PATH` and finds `/usr/bin/ls`.
    
4. **Bash creates a child process.**
    
    Typically using `fork()`, which creates a new process based on the shell.
    
5. **The child starts the executable.**
    
    Through `execve()`, the child becomes the `ls` program.
    
6. `ls` **requests directory information.**
    
    It makes filesystem-related system calls, handled by the kernel.
    
7. `ls` **writes its output.**
    
    The output passes through its stdout file descriptor into the terminal session.
    
8. **The process exits.**
    
    Bash collects its exit status and returns to displaying the prompt.
    

That is the complete basic lifecycle.

Now let's explore the individual pieces, because understanding them explains a huge amount of Linux, Docker, Node.js, backend services, and production debugging.

# **3. How does Bash actually understand your command?**

Consider this:

```
ls -la /home/ubuntu/projects
```

When you press Enter, Bash doesn't simply send that entire string to the kernel.

**Bash is an interpreter.** It has its own grammar, variables, expansions, command resolution rules, and execution mechanisms.

It processes the command before any external executable starts.

## **Step 1: Reading and parsing**

Bash reads your input and parses it according to shell syntax.

For this command:

```
ls -la /home/ubuntu/projects
```

It identifies three words:

```
ls
-la
/home/ubuntu/projects
```

There is no special Linux kernel mechanism that recognizes `-la` as an option.

To Bash, `-la` is simply an argument. The `ls` application decides that `-l` means long listing and `-a` means include hidden entries.

This distinction matters:

```
node --inspect server.js
```

The shell doesn't know what `--inspect` means. Node.js knows.

Similarly:

```
npm run start
```

Bash doesn't know what `run` or `start` means. Those are arguments passed to `npm`, which interprets them.

### **Shell syntax is different from program arguments**

Now consider:

```
ls -la > output.txt
```

The `>` character is shell syntax.

Bash interprets it as a request to redirect output to a file.

The `ls` executable normally never receives `>` or `output.txt` as arguments.

That is why:

```
echo hello > result.txt
```

and:

```
echo "hello > result.txt"
```

behave differently.

The first redirects output to a file. The second prints the text `hello > result.txt`.

Quotation changes how Bash interprets input.

## **Step 2: Expansion**

Before executing a simple command, Bash performs various expansions.

For example:

```
echo "$HOME"
```

Bash replaces `$HOME` with the value of that shell variable.

If your home directory is `/home/alex`, the command effectively supplies the argument:

```
/home/alex
```

Other examples:

```
echo *.js
echo "$(pwd)"
echo "$((10 + 20))"
```

These involve different expansions:

|Expression|What Bash does|
|---|---|
|`$HOME`|Parameter expansion|
|`*.js`|Filename expansion (globbing)|
|`$(pwd)`|Command substitution|
|`$((10 + 20))`|Arithmetic expansion|
|`~/projects`|Tilde expansion|

For example, suppose your directory contains:

```
server.js
app.js
package.json
```

When you run:

```
ls *.js
```

Bash normally expands `*.js` before launching `ls`.

The application receives something equivalent to:

```
ls app.js server.js
```

**The glob expansion was performed by Bash, not by** `ls`**.**

There are important subtleties here. Expansion is context-sensitive: quoted variables don't undergo the same word splitting and globbing as unquoted variables, and Bash performs some expansions before others.

That is why backend scripts should usually quote variable expansions:

```
rm -- "$filename"
```

rather than:

```
rm $filename
```

The second form can turn a filename containing spaces or wildcard characters into unexpected arguments.

# **4. How does Linux find** `ls`**,** `node`**,** `python`**, or** `npm`**?**

Consider:

```
node server.js
```

Where exactly is `node`?

You haven't specified:

```
/usr/bin/node
```

or:

```
/home/alex/.nvm/versions/node/v24.0.0/bin/node
```

You only typed `node`.

How does Bash know where to find it?

## **The** `$PATH` **environment variable**

Run:

```
echo "$PATH"
```

You'll see something similar to:

```
/home/alex/.nvm/versions/node/v24.0.0/bin:/usr/local/bin:/usr/bin:/bin
```

`PATH` is a colon-separated list of directories that Bash can search for executable commands.

When you run:

```
node server.js
```

Bash determines whether `node` is an alias, function, builtin, or executable. If it needs to search for an external executable, it consults the command-search rules, including `PATH`.

It might find:

```
/home/alex/.nvm/versions/node/v24.0.0/bin/node
```

It then arranges for that executable to start.

You can investigate:

```
type -a node
command -v node
readlink -f "$(command -v node)"
```

`type -a` is particularly useful because it shows how Bash can resolve a command, including functions, builtins, and external executables.

Bash also caches some executable locations in a command hash table to avoid repeated searches.

### **Why installing a program sometimes results in** `command not found`

Imagine you've installed a custom tool at:

```
/opt/mytools/bin/mycli
```

But your `PATH` doesn't include:

```
/opt/mytools/bin
```

Then:

```
mycli
```

may fail with:

```
mycli: command not found
```

The tool can exist on disk and still not be found by Bash.

Running:

```
/opt/mytools/bin/mycli
```

uses an explicit pathname and bypasses the normal `PATH` search.

Alternatively, you can add its directory to `PATH`:

```
export PATH="/opt/mytools/bin:$PATH"
```

This changes the environment for the current shell and subsequently launched processes.

One more subtlety: unlike some other systems, Bash does not normally search the current directory automatically. That's why you often use:

```
./my-script
```

instead of:

```
my-script
```

The `./` explicitly refers to a pathname relative to the current working directory.

# **5. The most important concept: processes**

You've probably encountered processes, process IDs, and process managers. But what is a process from the operating system's perspective?

A **process is a running instance of a program, together with the execution state and resources that the operating system manages for it.**

A program is code stored somewhere, usually in a file.

A process is the live execution context.

For example:

```
node server.js
```

starts a Node.js process.

```
node worker.js
```

starts another Node.js process.

Both can use the same Node.js executable, but they have their own process identities, memory spaces, execution states, and resource tables.

**Node.js executable on disk**

/usr/bin/node

**Process A**

PID 4102

node server.js

Own virtual memory, file descriptors, sockets, execution state

**Process B**

PID 4103

node worker.js

Separate virtual memory, file descriptors, sockets, execution state

Both processes may share physical memory pages containing executable code, but they have separate virtual address spaces.

This matters because changing a normal JavaScript variable in one Node.js process does not automatically change the corresponding variable in another process.

You need an explicit communication or shared-state mechanism, such as a socket, pipe, shared memory, database, or message broker.

## **Every process has a PID**

PID means _Process ID_.

For example:

```
ps aux | grep node
```

Might show:

```
alex  4102  ... node server.js
alex  4103  ... node worker.js
```

Here `4102` and `4103` are different processes.

You can inspect a particular process:

```
ps -p 4102 -o pid,ppid,stat,comm,args
```

The `PPID` is the parent process ID.

Processes commonly form parent-child relationships.

For example:

**Terminal session**

**Bash · PID 3000**

**Node · PID 4102**

PPID 3000

**ls · PID 4103**

PPID 3000

Illustrative process relationship, not a snapshot of simultaneously running foreground jobs.

A shell may create child processes to execute commands. Those children may create further children.

This is fundamental to understanding process supervision, worker processes, and containers.

# **6. What exactly happens during** `fork()`**?**

Now we reach one of the most interesting parts.

Suppose Bash is running with PID `3000`.

You enter:

```
ls -la
```

Bash has already parsed the command and found the executable.

For a normal foreground external command, Bash typically calls:

```
fork();
```

This is a system call that creates a child process.

Conceptually:

**Bash process**

PID 3000

fork()

**Parent**

Bash · PID 3000

Continues as Bash

**Child**

Bash-derived · PID 3001

New process

After `fork()`:

- There are two processes.
    
- The parent retains its existing PID.
    
- The child has a new PID.
    
- The child initially has a copy of the parent's virtual-memory state.
    
- The child inherits copies of the parent's file descriptors, which refer to the same underlying open-file descriptions.
    
- Both processes continue from the point after the `fork()` call.
    

In C, this distinction is visible through the return value:

```
pid_t pid = fork();

if (pid == 0) {
    // Child process
} else if (pid > 0) {
    // Parent process
} else {
    // fork() failed
}
```

The parent receives the child's PID, while the child receives `0`.

This allows the same program to execute different logic in the parent and child.

## **Does** `fork()` **duplicate all of Bash's memory?**

Logically, the child begins with a copy of the parent's memory state.

But Linux normally uses an optimization called **copy-on-write (COW)**.

Instead of immediately duplicating every memory page, the parent and child can initially reference the same physical pages.

If one process modifies a page that must remain private, the kernel creates a private copy as necessary.

So you don't generally pay the cost of copying the entire shell's memory at the instant of `fork()`.

This is one reason the fork-and-exec model is practical.

## **Why doesn't** `fork()` **start** `ls` **immediately?**

Because `fork()` only creates a new process.

At that point, the child is still running the shell's program code.

To run `ls`, something else must happen.

That brings us to `execve()`.

# **7. What exactly happens during** `execve()`**?**

In the child process, Bash typically arranges a call resembling:

```
execve(
    "/usr/bin/ls",
    argv,
    envp
);
```

The arguments are conceptually:

```
argv = {
    "ls",
    "-la",
    NULL
};
```

And `envp` contains the environment passed to the program.

`execve()` tells the kernel:

**Replace this process's currently running program with the executable at this path.**

The crucial detail:

`execve()` does not create another process.

The child retains its PID.

**Parent**

PID 3000

Bash remains Bash

**Child before exec**

PID 3001

Bash-derived execution

execve("/usr/bin/ls", ...)

**Parent**

PID 3000

Bash

**Child after exec**

PID 3001

Now running `ls`

In a successful execution, the kernel replaces the process's program memory mappings with those required for the new executable.

For a typical dynamically linked Linux program, the kernel also starts the ELF interpreter (dynamic linker) specified by the executable. The linker loads required shared libraries, performs linking work, and transfers control to the program's startup code.

Eventually, the application's `main()` function runs.

The new program receives arguments and its environment and usually retains file descriptors that weren't marked close-on-exec.

This is why `ls` can write to the same terminal session that Bash was using.

### **The subtle difference between fork and exec**

|Operation|`fork()`|`execve()`|
|---|---|---|
|Creates new process|Yes|No|
|Creates new PID|Yes|No|
|Retains existing program image|Initially, yes|No, replaces it|
|Parent process continues|Yes|Not applicable|
|Used to launch external commands|Typically before exec|Starts the new program|

A useful mental model is:

**Fork creates another process. Exec changes which program a process runs.**

These are separate capabilities.

There are optimizations and alternatives, such as `posix_spawn()` and `vfork()`-style implementations. But fork followed by exec is the classic Unix process-creation model, and it is an accurate way to understand ordinary Bash command execution.

# **8. What happens to Bash while** `ls` **is running?**

Now we have:

```
Bash  PID 3000
  └── ls  PID 3001
```

When you run:

```
ls -la
```

you normally expect Bash to wait for `ls` to finish before showing the next prompt.

The parent shell uses a waiting mechanism such as:

```
waitpid(child_pid, &status, 0);
```

Underneath, Linux provides wait-family system calls such as `wait4()`.

The kernel manages process scheduling. Bash does not need to spin in an infinite loop checking whether `ls` has finished.

The shell can sleep while waiting for a change in its child's state.

Meanwhile, the kernel schedules `ls` when it is runnable.

## **What happens when** `ls` **finishes?**

The `ls` program eventually terminates, usually with an exit status.

For example:

```
ls /tmp
echo $?
```

If successful, `ls` normally exits with status `0`.

If there is an error, it uses a nonzero exit status.

For example:

```
ls /directory-that-does-not-exist
echo $?
```

The second command displays the previous command's exit status.

When the child exits:

1. The kernel records its termination status.
    
2. The parent shell is notified that the child's state has changed.
    
3. Bash collects the status using a wait operation.
    
4. Bash updates its own command-status state.
    
5. Bash displays the next prompt.
    

A terminated child whose exit status hasn't yet been collected is called a **zombie process**. It isn't still executing; its remaining process record exists so the parent can obtain its termination status.

This is relevant to backend engineering because a poorly implemented process supervisor can accidentally accumulate zombie child processes.

## **What about** `&`**?**

Compare:

```
sleep 30
```

with:

```
sleep 30 &
```

In the first case, the shell normally waits for the foreground command.

In the second case, Bash starts an asynchronous job and generally returns to the prompt without waiting for the job to finish.

Try:

```
sleep 30 &
echo $!
jobs
```

`$!` gives the PID of the last asynchronous job started in the relevant shell context.

`jobs` lists jobs tracked by the current shell.

The process exists independently of Bash's interactive prompt, but don't confuse background execution with full detachment. Depending on the shell, terminal session, and signal handling, background processes may still be affected when the terminal closes.

That's why production services normally use a supervisor such as systemd rather than simply appending `&`.

# **9. Why is** `cd` **different from** `ls`**?**

This is one of the best examples of why the shell/process distinction matters.

You enter:

```
cd /var/log
```

What happens?

Unlike `ls`, `cd` is normally a Bash builtin.

It executes within the Bash process itself.

Why?

Because **the current working directory is part of a process's operating-system state.**

Every process has a current working directory (CWD).

When a program opens a relative pathname like:

```
config.json
```

the kernel resolves it relative to that process's current working directory, unless the program uses another directory as its reference.

Suppose your shell has:

```
PID: 3000
CWD: /home/alex
```

When you type:

```
cd /var/log
```

Bash invokes the kernel operation:

```
chdir("/var/log");
```

The kernel checks whether the target directory is accessible and whether the operation is valid.

If successful, the shell process's CWD changes.

```
Before:
Bash PID 3000
CWD /home/alex

After:
Bash PID 3000
CWD /var/log
```

Bash may also update shell variables such as `PWD` and `OLDPWD`.

The process is still Bash, with the same PID.

## **Why can't an ordinary external executable change your shell's directory?**

Imagine Bash launches a child process:

```
Bash PID 3000
  └── Child PID 3001
```

The child inherits the parent's working directory initially.

If the child calls:

```
chdir("/var/log");
```

it changes **its own** current working directory.

It doesn't change its parent's current working directory.

So after the child exits, Bash is still in its original directory.

That's why `cd` must run in the current shell context if its effect is supposed to persist.

### **Prove it on Ubuntu**

Run:

```
pwd
(cd /tmp && pwd)
pwd
```

Suppose you started in `/home/alex`.

You'll see something like:

```
/home/alex
/tmp
/home/alex
```

Why?

The parentheses create a subshell environment.

The `cd /tmp` operation changes the directory in that subshell context, not the parent Bash process.

Contrast that with:

```
cd /tmp
pwd
```

Now the directory change persists.

This also explains a common misconception:

```
cd /tmp | cat
```

Does not change your interactive shell's directory because pipeline elements normally run in subshell environments in Bash (with some documented exceptions).

# **10. How do programs print output to your terminal?**

Now let's move to file descriptors.

Every typical Linux process starts with three conventional file descriptors.

|   |   |   |
|---|---|---|
|**FD**|**Name**|**Purpose**|
|0|stdin|Standard input|
|1|stdout|Standard output|
|2|stderr|Standard error|

These are conventions, not a guarantee that every process always has all three descriptors open.

A file descriptor is a small integer identifying an open I/O resource within a process.

The resource does not have to be an ordinary file.

It may refer to a terminal, pipe, socket, device, or other kernel-managed object.

## **Example:** `echo hello`

When you enter:

```
echo hello
```

Bash normally executes its builtin `echo`.

Internally, an output operation ultimately results in something analogous to:

```
write(1, "hello\n", 6);
```

Here:

- `1` is stdout.
    
- `"hello\n"` is the data being written.
    
- `6` is the number of bytes.
    

Because stdout is connected to your terminal session, the data travels through the PTY to the terminal emulator.

The terminal emulator renders it on the screen.

**Running program**

write(1, "hello\n", 6)

**Kernel / stdout file descriptor**

**PTY**

**Terminal emulator displays** `hello`

For a command such as `ls`, the standard-output path is essentially the same. The program writes output; the kernel routes the data according to what its stdout descriptor references.

The program doesn't need to know whether you are using GNOME Terminal, another terminal emulator, or SSH.

## **How does output redirection work?**

Consider:

```
ls -la > output.txt
```

Bash arranges for stdout to point to `output.txt` instead of the terminal.

For an external command, the child setup conceptually resembles:

```
int fd = open("output.txt", O_WRONLY | O_CREAT | O_TRUNC, 0666);

dup2(fd, STDOUT_FILENO);
close(fd);

execve("/usr/bin/ls", argv, envp);
```

This is simplified C-like pseudocode; Bash also handles errors, permissions, and other execution details.

The important part is:

```
dup2(fd, 1);
```

It arranges for file descriptor `1` to refer to the opened output file.

Then `execve()` launches `ls`.

When `ls` writes to stdout, the data goes to the file.

**The** `ls` **program doesn't need to know that Bash redirected its output.**

It just writes to FD 1.

### **Why** `>` **and** `>>` **behave differently**

```
echo hello > output.txt
```

Normally opens the file in a mode that truncates it.

```
echo hello >> output.txt
```

Normally opens the file in append mode.

In both cases, the shell handles the redirection.

### **Why errors sometimes still show in the terminal**

Try:

```
ls /does-not-exist > output.txt
```

The error may still appear on your terminal.

Why?

Because `>` redirects stdout (FD 1), but errors are generally written to stderr (FD 2).

Try:

```
ls /does-not-exist 2> error.txt
```

Now stderr goes to `error.txt`.

Or:

```
ls /does-not-exist > output.txt 2>&1
```

Now stdout goes to `output.txt`, and stderr is duplicated from stdout's current destination.

Redirection order matters:

```
command > file 2>&1
```

is not always equivalent to:

```
command 2>&1 > file
```

The shell processes redirections left to right.

This becomes important when debugging systemd services, Docker logs, build scripts, and CI/CD pipelines.

# **11. How do pipes actually work?**

Consider:

```
cat access.log | grep "ERROR" | wc -l
```

This is incredibly common in Linux.

But what exactly happens?

You have three programs:

```
cat
grep
wc
```

Bash needs to connect them so that the output of one becomes the input of the next.

It uses kernel pipes.

**cat access.log**

Reads the file and writes matching input stream

stdout (FD 1)

Kernel pipe A

**grep "ERROR"**

Reads stdin and outputs matching lines

stdin (FD 0) → stdout (FD 1)

Kernel pipe B

**wc -l**

Counts lines from stdin

stdin (FD 0)

**Terminal output**

Final count

Here's what Bash does conceptually:

1. Creates two pipes using the kernel.
    
2. Starts the pipeline commands as separate processes.
    
3. Connects `cat` stdout to pipe A's write end.
    
4. Connects `grep` stdin to pipe A's read end and stdout to pipe B's write end.
    
5. Connects `wc` stdin to pipe B's read end.
    
6. Closes unneeded pipe descriptors in the processes.
    
7. Waits for the foreground pipeline to complete.
    

The kernel maintains the pipes, including their data buffers and blocking/wakeup behavior.

When `cat` writes, `grep` can read that data.

When `grep` writes, `wc` can read that data.

### **They don't need to run one after another**

This is a key insight.

`cat`, `grep`, and `wc` can all be running concurrently.

The output flows as a stream.

You don't have to wait for `cat` to finish the entire file before `grep` can start processing.

If a pipe fills, the writer may block until the reader consumes data. This is called **backpressure**.

As a backend engineer, you should recognize this concept from stream processing.

It's the same general problem you encounter with Node.js streams, queues, network sockets, and high-volume data pipelines.

### **What happens when the reader stops?**

Suppose:

```
cat huge.log | head -n 5
```

`head` only needs five lines, then normally exits.

When the pipe has no remaining readers, a subsequent write by `cat` can trigger `SIGPIPE`, or fail with `EPIPE` when that signal is ignored or handled.

That's one reason a pipeline can cause an upstream program to stop early.

# **12. Environment variables: how child processes receive configuration**

Let's take this command:

```
NODE_ENV=production node server.js
```

What happens to `NODE_ENV`?

Bash parses the assignment as part of the command's environment setup.

It arranges for the Node.js process to receive:

```
NODE_ENV=production
```

Node can then read it:

```
console.log(process.env.NODE_ENV);
```

Output:

```
production
```

The environment is a set of key-value strings supplied to a process when it starts.

For a native program, `execve()` takes the environment as an argument.

## **Shell variable vs exported environment variable**

Consider:

```
MY_VALUE=hello
```

This creates or updates a Bash variable.

But it isn't necessarily exported to child processes.

Compare:

```
MY_VALUE=hello
node -e 'console.log(process.env.MY_VALUE)'
```

This typically prints:

```
undefined
```

Now:

```
export MY_VALUE=hello
node -e 'console.log(process.env.MY_VALUE)'
```

This prints:

```
hello
```

Why?

Because `export` marks the shell variable for inclusion in the environments of subsequently started programs.

The child receives its own environment state. If it changes that environment, the parent shell's environment doesn't automatically change.

## **Why** `.env` **files don't automatically affect your shell**

Suppose `.env` contains:

```
DATABASE_URL=postgresql://localhost:5432/mydb
PORT=3000
```

The file itself is only text.

Neither Linux nor Bash automatically loads an arbitrary `.env` file when starting Node.

Your application might load it with a library such as `dotenv`, or an external tool or supervisor might arrange the environment before launching Node.

If you explicitly source a trusted, shell-compatible file:

```
source .env
```

Bash executes its contents as shell code in the current shell.

That is not the same as a dedicated `.env` parser. It also means sourcing an untrusted file can execute arbitrary shell commands.

If the variables need to be passed to child processes, they must be exported, either individually or through an appropriate setup.

# **13. What does the kernel actually do while** `ls` **reads a directory?**

We have understood how `ls` starts.

Now the program needs to obtain directory information.

Imagine you're in:

```
/home/alex/projects
```

and run:

```
ls -la
```

The executable needs information about the directory entries and, for `-l`, additional file metadata.

On Linux, you may see operations involving syscalls such as:

```
openat()
getdents64()
newfstatat()
write()
```

The exact calls vary with the program version, options, library implementation, and environment.

Conceptually:

- `openat()` can open the directory.
    
- `getdents64()` can retrieve directory entries.
    
- `newfstatat()` can retrieve metadata such as permissions and sizes.
    
- `write()` can output the formatted results.
    

## **Where does the filesystem fit in?**

The kernel maintains a Virtual Filesystem (VFS) layer, providing a common interface for different underlying filesystems.

Your files might be stored on ext4, XFS, Btrfs, tmpfs, or another supported filesystem.

Applications don't need separate implementations for each one.

**ls / application**

**System calls**

openat, getdents64, newfstatat

**Kernel VFS**

ext4

XFS

tmpfs

**Storage / memory-backed data**

The VFS resolves pathnames, checks access requirements, and dispatches operations to the relevant filesystem implementation.

The directory data may already be in the kernel's caches. In that case, Linux doesn't necessarily need to access the physical storage device for every `ls` execution.

If data must be fetched from storage, the kernel coordinates the necessary I/O.

This is one reason reading a file twice can be dramatically faster than reading it the first time.

Linux's page cache is often involved.

## **File permissions are checked by the kernel**

Consider:

```
cat /etc/shadow
```

On a typical Ubuntu system, an ordinary user cannot read this file.

`cat` can try to open it, but the kernel enforces access controls.

The system call may fail with an error such as:

```
EACCES
```

The program then converts the error into something human-readable, such as:

```
Permission denied
```

Changing the command's text doesn't bypass kernel permissions.

And the executable permission bit is separate from file read permission: executing a binary usually requires execute access to the file and search access to its containing directories, while reading a file requires the appropriate read access.

# **14. What really happens when you run** `node server.js`**?**

Now let's connect all of this to backend engineering.

Suppose you have an Express application:

```
const express = require("express");

const app = express();

app.get("/hello", (req, res) => {
  res.json({ message: "Hello" });
});

app.listen(3000, () => {
  console.log("Server listening on port 3000");
});
```

You type:

```
node server.js
```

The first part is the same as executing `ls`.

Bash resolves `node`, starts a child process, and arranges for Node.js to execute.

But what happens after that?

## **Phase A: Node.js starts**

The kernel loads the executable and starts it.

Node initializes its runtime, including components such as:

- The V8 JavaScript engine.
    
- The event loop and libuv.
    
- The necessary runtime libraries.
    
- Internal structures for JavaScript execution and asynchronous operations.
    

Node reads `server.js` and executes your JavaScript.

Express registers the HTTP route.

Nothing has made a network request yet.

## **Phase B: Your server starts listening**

When your code executes:

```
app.listen(3000);
```

Node eventually makes requests to the kernel to create and configure a listening TCP socket.

Conceptually, this involves syscalls such as:

```
socket();
bind();
listen();
```

Here's the flow:

**Express application**

app.listen(3000)

**Node.js / libuv**

**Kernel networking stack**

Socket, bind, listen

**Listening TCP socket**

Port 3000

The kernel maintains the socket and TCP protocol state.

Node manages application-level behavior.

The kernel handles lower-level networking operations, including packet delivery, TCP acknowledgments, buffering, and connection state.

When a client connects, Node accepts connections and handles data through its event-driven runtime.

Your HTTP route handler eventually executes when Node has received and parsed the corresponding request.

The key idea is:

**Node doesn't implement the entire networking stack itself. It uses kernel-provided sockets to communicate over the network.**

## **Phase C: Why doesn't the command finish?**

When you run:

```
ls
```

the program completes its work and exits.

When you run:

```
node server.js
```

your process continues running because the server has active work to maintain, notably its listening socket.

The Node.js event loop can continue waiting for and responding to events.

Bash, meanwhile, is normally waiting for the foreground Node process.

That's why your prompt doesn't return.

Your Node process may consume very little CPU while idle, because it can wait efficiently for events instead of continuously checking whether a client has connected.

# **15. What happens when you press Ctrl+C?**

This part is particularly useful for backend engineers.

You have a server running:

```
node server.js
```

You press:

```
Ctrl+C
```

It might seem as though the terminal directly kills Node.

That's not quite what happens.

In a normal interactive terminal session, the terminal's line discipline interprets Ctrl+C as an interrupt character.

The kernel sends `SIGINT` to the foreground process group associated with the terminal.

This is an important concept: **signals**.

Signals are a Unix/Linux mechanism for notifying processes about events.

Common examples:

|Signal|Typical purpose|
|---|---|
|`SIGINT`|Interrupt, often caused by Ctrl+C|
|`SIGTERM`|Request graceful termination|
|`SIGKILL`|Force termination; cannot be caught or ignored|
|`SIGSTOP`|Stop a process; cannot be caught or ignored|
|`SIGCONT`|Resume a stopped process|
|`SIGHUP`|Hangup notification, often associated with terminal/session closure|
|`SIGCHLD`|Notify a parent about child state changes|

A process may install handlers for catchable signals.

For example, in Node.js:

```
process.on("SIGINT", () => {
  console.log("Received SIGINT");
  process.exit(0);
});
```

Now Ctrl+C triggers the handler under normal conditions.

For a backend service, you would usually shut down more carefully:

```
const server = app.listen(3000);

process.on("SIGTERM", () => {
  console.log("Shutting down");

  server.close(() => {
    console.log("Server closed");
  });
});
```

`SIGTERM` is especially relevant to services running under systemd, Docker, or Kubernetes.

The service manager generally tries to request shutdown before forcefully terminating a process after some grace period.

## **What about Ctrl+Z?**

When you press Ctrl+Z in a normal interactive terminal, the foreground job receives `SIGTSTP`.

The job is suspended rather than terminated.

You can then use:

```
jobs
fg
bg
```

to examine, resume in the foreground, or resume in the background.

This works because interactive shells implement **job control** using process groups and terminal foreground ownership.

So there are three different things:

- A process: an executing program instance.
    
- A process group: a group of processes, useful for signals and job control.
    
- A terminal session: the environment through which interactive input/output and foreground job control occur.
    

They're related, but not interchangeable.

# **16. Why can a Bash script affect your environment sometimes, but not always?**

Consider a script named `setup.sh`:

```
#!/usr/bin/env bash

export APP_ENV=development
cd /tmp
```

Now execute:

```
bash setup.sh
```

What happens?

Your existing Bash typically creates a new process to run another Bash interpreter.

That child Bash executes the script.

It changes its environment and current directory.

Then it exits.

Your original interactive shell is unchanged.

Now compare:

```
source setup.sh
```

or:

```
. setup.sh
```

Here, the current Bash reads and executes the script's commands.

So the variable and directory changes persist.

|Command|What happens|
|---|---|
|`bash setup.sh`|A Bash process runs the script separately|
|`./setup.sh`|The executable script is launched, using its interpreter directive|
|`source setup.sh`|The current Bash executes the script's contents|
|`exec node server.js`|The current shell process is replaced by Node.js|

That last one is particularly interesting.

## **What does the Bash** `exec` **builtin do?**

Try to understand this:

```
exec node server.js
```

Unlike a normal external command, Bash doesn't need to preserve a parent shell to return to afterward.

It replaces its own process image with Node.js.

The PID can remain the same.

Once Node exits, that shell is gone.

This is useful in container entrypoint scripts:

```
#!/bin/sh

echo "Starting application"
exec node server.js
```

Using `exec` here can make Node the main process rather than leaving an unnecessary shell process supervising it.

This can simplify signal delivery and lifecycle behavior.

# **17. How does this relate to Docker?**

You've probably run:

```
docker run node:24
```

or:

```
docker exec -it my-container sh
```

Docker doesn't replace Linux's process model with a different one.

Containers still use Linux processes, memory, file descriptors, sockets, signals, and system calls.

The main difference is that Linux isolation and resource-management features are applied to those processes.

Important mechanisms include:

- **Namespaces:** isolate views of resources such as process IDs, mount points, and network interfaces.
    
- **Cgroups:** organize and limit resource usage such as CPU and memory.
    
- **Filesystem mounts:** provide the container's filesystem view.
    

For example, a process inside a container might appear to have PID `1` inside its PID namespace while having a different PID from the host's perspective.

When Node calls:

```
openat(...)
```

inside a Linux container, it's still calling into the host Linux kernel.

The kernel applies the relevant namespace, permission, and filesystem context.

There's no separate full Linux kernel running for every ordinary Docker container.

That distinction explains a lot of Docker behavior.

# **18. How can you actually observe all of this on Ubuntu?**

This is where everything starts to become concrete.

I recommend running the following experiments on your own Ubuntu development machine.

## **Experiment 1: Observe command resolution**

```
type -a ls
type -a cd
type -a node
```

You might see:

```
ls is /usr/bin/ls
cd is a shell builtin
node is /usr/bin/node
```

Results vary depending on aliases, functions, and your installed tools.

This verifies that Bash doesn't execute every command in the same way.

## **Experiment 2: Observe your shell process**

```
echo $$
ps -p $$ -o pid,ppid,stat,comm,args
```

The first command outputs your shell's PID.

The second displays information about that process.

Now run:

```
sleep 60 &
echo $!
ps -o pid,ppid,stat,comm -p "$!"
```

You can see the background `sleep` process and its parent relationship.

Then clean up:

```
kill "$!"
wait "$!" 2>/dev/null
```

## **Experiment 3: Observe file descriptors**

Run:

```
ls -l /proc/$$/fd
```

`/proc` is a kernel-provided virtual filesystem that exposes process information.

The `/proc/<PID>/fd` directory contains entries corresponding to that process's open file descriptors.

You might see entries resembling:

```
0 -> /dev/pts/2
1 -> /dev/pts/2
2 -> /dev/pts/2
```

Your output may differ.

`/dev/pts/2` is a pseudoterminal device.

This demonstrates that stdin, stdout, and stderr are connected to your terminal session.

## **Experiment 4: Observe syscalls with** `strace`

This is probably the most valuable experiment.

Install `strace` if necessary:

```
sudo apt install strace
```

Then run:

```
strace -e trace=execve,openat,getdents64,write ls -la
```

You'll see lines resembling:

```
execve("/usr/bin/ls", ["ls", "-la"], ...) = 0
openat(AT_FDCWD, ".", ...) = 3
getdents64(3, ..., 32768) = ...
write(1, "...", ...) = ...
```

The output varies depending on Ubuntu, your installed libraries, and the program version.

But notice something beautiful here.

You're observing the actual boundary between a user-space program and the Linux kernel.

`strace` shows you system calls and their results.

You can directly see how a seemingly simple command interacts with the OS.

### **Can you observe fork and exec as well?**

Yes.

Try:

```
strace -f -e trace=process bash -c 'ls >/dev/null'
```

This runs a new Bash instance under tracing.

The `-f` option follows processes created by the traced program.

Depending on your system, you may see calls such as:

```
execve("/usr/bin/bash", ...)
clone(...)
[child] execve("/usr/bin/ls", ...)
wait4(...)
```

This is particularly instructive because it exposes process creation, program execution, and waiting.

You can observe the process lifecycle rather than imagining it.

## **Experiment 5: Explore the running Node process**

Start a Node server in one terminal:

```
node server.js
```

Then open a second terminal.

Find its PID:

```
pgrep -af 'node server.js'
```

Suppose the PID is `4102`.

Inspect it:

```
ps -p 4102 -o pid,ppid,stat,%cpu,%mem,args
```

Inspect its working directory:

```
readlink /proc/4102/cwd
```

Inspect its executable:

```
readlink /proc/4102/exe
```

Inspect its file descriptors:

```
ls -l /proc/4102/fd
```

Check listening sockets:

```
ss -ltnp
```

Look for your server's port.

If you run `strace` on a development process you own, you can also examine network operations, although tracing can affect timing and performance.

For example:

```
strace -e trace=network node server.js
```

Then send a request from another terminal:

```
curl http://127.0.0.1:3000/hello
```

You'll get an opportunity to see the relationship between your HTTP server and the kernel's networking interface.

# **19. One complete mental model**

Imagine you enter this command:

```
NODE_ENV=production node server.js > app.log 2>&1
```

Here's what happens, bringing all the concepts together.

1. **Terminal input:** Your terminal session delivers the command text to Bash.
    
2. **Parsing:** Bash identifies a variable assignment, an external command, its argument, and output redirections.
    
3. **Command resolution:** Bash locates the Node.js executable using its command resolution rules.
    
4. **Process creation:** Bash normally forks a child process.
    
5. **I/O setup:** The child opens `app.log`, makes stdout reference it, and duplicates stdout onto stderr.
    
6. **Environment setup:** The new program is given `NODE_ENV=production` along with the rest of its configured environment.
    
7. **Program execution:** `execve()` replaces the child process image with Node.js.
    
8. **Runtime initialization:** Node initializes its runtime and executes `server.js`.
    
9. **Networking:** Node requests a listening socket from the kernel.
    
10. **Event processing:** Node waits for connections and processes incoming requests.
    
11. **Logging:** Output written to stdout or stderr is sent to `app.log` through the file descriptors configured earlier.
    
12. **Termination:** When the process eventually exits, Bash collects its exit status and can display another prompt.
    

What's remarkable is that a single command line is coordinating all those mechanisms.

The shell is not executing JavaScript.

The terminal is not implementing TCP.

Node is not manually reading from physical storage.

Each component has a responsibility.

# **20. The concepts I'd prioritize as a backend engineer**

You don't need to study processor instruction sets or memory bytes to gain a strong understanding of Linux.

But these subjects are worth knowing in detail:

|Concept|Why it matters in backend engineering|
|---|---|
|Processes and threads|Node workers, clustering, concurrency, service architecture|
|`fork()` and `execve()`|Starting child processes, shell execution, process managers|
|File descriptors|Logging, sockets, pipes, resource leaks|
|stdin/stdout/stderr|Docker logging, CI/CD, shell scripting|
|Environment variables|Configuration, deployment, secret management|
|Signals|Graceful shutdown, systemd, Kubernetes|
|Linux permissions|Deployment failures, service accounts, file access|
|Virtual memory|Memory isolation, memory leaks, OOM issues|
|Filesystem and page cache|Database I/O, disk performance|
|Sockets and TCP|APIs, connection handling, reverse proxies|
|Process scheduling|CPU usage, concurrency, latency|
|Namespaces and cgroups|Docker and container isolation|

## **The single most important takeaway**

When you type a command in Ubuntu, you are **asking a user-space program called the shell to interpret your instructions and coordinate other processes and operating-system services**.

The kernel doesn't understand commands like `npm start`, `cd`, or `ls` as shell commands.

It understands lower-level operations such as creating processes, loading executables, opening files, writing data, managing memory, and communicating over sockets.

The shell translates your command-line intentions into a sequence of operations, while the programs it launches perform their own work through kernel interfaces.

Once you really understand the boundary between **terminal → shell → process → syscall → kernel**, things like Docker, Node.js process management, Bash scripting, Linux permissions, and backend infrastructure become much easier to reason about.

And the best tool for making this understanding real is `strace`: you can watch the operating-system operations happen instead of only reading about them.