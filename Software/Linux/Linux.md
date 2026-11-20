In linux family distribution means the group of linux distros that share a common base, package management systems and core design philosophy.

###### Linux Distributions
![[Pasted image 20250320203245.png]]
###### Package Manager
Linux software comes in packages(files containing programs and all their parts). Package manager helps us to prevent having to use those files manually to install software. Instead they find the software you want to online repositories(like play store), download the right version, install, keep it updated and remove.  
###### Red Hat Family
Read Hat Enterprise Linux heads the family that includes CentOS, CentOS Stream, Fedora and Oracle Linux. Ironically, Fedora has more software than Read Hat Enterprise Version and the reason is the diverse community is involved in building Fedora, so that contributors that don't work in RHEL also are contributing. And it is also used as testing platform for future RHEL releases. CentOS is also used for testing. 

So First Fedora is used for bleeding edge testing. and CentOS Stream(CentOS is discontinued from 2021) takes what is somewhat stable from Fedora and RHEL takes from CentOS. So CentOS is close to RHEL than fedora.. Fedora is too much bleeding edge to teach or use. so people may prefer CentOS. Especially corporates and teachers may prefer CentOS as it is more stable. So Fedora is where new technologies are introduced. And CentOS takes those some of stable changes and gives preview of what RHEL takes. RHEL just takes battle tested features from CentOS. and harden them for Enterprise uses. 

Some key features for Red Hat Family. 
- RHEL is used by enterprise which host their own system.
- Fedora serves as upstream testing platform for RHEL.(upstream means source project where development happens. and whatever takes that codes and add its own version is called downstream).
- CentOS is close clone of RHEL. Remember RHEL does cost money as they are used in enterprise and the corporation takes money for official support. However CentOS is close to RHEL but it doesn't costs money and doesn't have official support. RHEL is subscription based Linux distro. You pay for official support, security updates and certifications. 
- It supports multiple hardware platforms, means they can work on multiple hardware like intel/AMD CPUs, ARM processors and more so they are flexible. 
- It uses dnf(Dandified Yum)(tool to manage software packages like apt in debian), the RPM-bases package manager to install, update and remove packages in the system.
- Heavily patched version 4.18 kernel is used in RHEL/CentOS 8 Stream, while version 5.14 is used in RHEL/CentOS 9 Stream. Kernel is the core of the OS - it handles communication between hardware(CPU, memory, devices) and software. and Linux kernel have version just like in normal software releases. So, RH family takes plain Linux kernel and improve stability, some features and security of it and used in their own RHEL distribution and CentOS.

###### SUSE family
The relationship between SUSE (SUSE Linux Enterprise Server, or SLES) and openSUSE is similar to the one in Red hat family. like SLES costs money and mostly openSUSE is used as it cost less in terms of money. Just like in CentOS Stream and RHEL, here also SLES and openSUSE are nearly same but without official support and extra perks that is it.

Features
- It is used in retail 
- The kernel version is 5.14 is used in openSUSE Leap 15.4. Leap is more enterprise version of openSUSE.
- YAST(Yet another Setup Tool) is used for system administration purposes like installing software to configuring network, users and services. 
- OpenSUSE and SLES uses zypper, just like how dnf or apt is used. 
###### Debian Family
The Debian distribution is upstream for several other distributions, including Ubuntu. Debian is pure open source community project. Ubuntu is upstream for Linux Mint(Linux Mint may add some thing and disable some Ubuntu settings or experiences).
Features
- It uses the DPKG-bases APT package manger(using apt, apt-get, apt-cache, etc.), to install update and remove packages in the system.
- kernel version 5.19 is used in Ubuntu 22.04 LTS. 

## Philosophy
Unix is all about doing small task correctly. 
## Architecture
## Process
## File Management
## Identity & Permissions
## Bash
Bash find the command and Linux Kernel helps execute it. Bash uses an environment variable called `PATH` to find external commands. when we do 
```
echo $PATH
```
we get list of directories that is seperated by :. 

`$PATH` is mainly for locating external commands.
## Shell and Bash
When we are communicating with keyboard in our computer, we aren't directly talking to linux. First the level is Terminal emulator like GNOME terminal , Konsole and things like that. 
After that it is usually bash in ubuntu. 
after that it is program and system calls. command execution and request for OS services. 
After that is it linux kernel like process, memory, file system, scheduling I/O.
After that is it level. 

However. Programs and the shell both interact with the kernel; the layers aren't a strict one-way chain

Terminal Emulator. 
It doesn't interpret the command, It is just an interface between us and the program running inside terminal session. 

Shell
Bash identifies the command name `ls` and its arguments, resolves what `ls` refers to, and arranges its execution.
For an external program, the shell typically creates a child process and starts the executable.
For a shell builtin such as `cd`, the shell handles the operation itself.
This difference is extremely important.

Linux Kernel
The kernel is the core of the operating system.
It manages the resources that running programs need, including:
- Processes and CPU scheduling.
- Virtual memory.
- Files and directories.
- File permissions.
- Network sockets.
- Devices and I/O.
A shell cannot directly read arbitrary disk hardware or allocate its own physical memory however it wants. Instead, it asks the kernel to perform operations through an interface called **system calls (syscalls)**.

#### **2. What happens when you type** `ls -la` **and press Enter?**

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

#### How does Bash actually understand your command?**

Consider this:

```
ls -la /home/ubuntu/projects
```

When you press Enter, Bash doesn't simply send that entire string to the kernel.

**Bash is an interpreter.** It has its own grammar, variables, expansions, command resolution rules, and execution mechanisms.

It processes the command before any external executable starts.\

#### **Step 1: Reading and parsing**

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

Bash determines whether `node` is an alias, function, builtin, or executable

Running:

```
/opt/mytools/bin/mycli
```

uses an explicit pathname and bypasses the normal `PATH` search.

```
Bash built in command like cd
```

Every process has a current working directory(CWD). so `cd` is different from `ls`.

Unlike `ls`, `cd` is normally a Bash builtin.

It executes within the Bash process itself.

Why?

Because **the current working directory is part of a process's operating-system state.**

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

#### **Why can't an ordinary external executable change your shell's directory?**

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
## Networking
`ip`, `ping`, `ss`, `dig`, `curl`, `traceroute`, `tcpdump`
## Package Management
## Services
## Logs
## Disks and Memory Management
## Text Editors



