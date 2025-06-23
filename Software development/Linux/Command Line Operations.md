#### Command-Line Mode Options
Command line interface makes difficult tasks possible while GUI makes easy tasks easier. The advantages of CUI are:
- You can sign into remote machines anywhere on the internet
- CUI doesn't vary among Linux distro. while GUI does. 
- Virtually any and every tasks can be accomplished while in CUI.

###### Using the text terminal on the Graphical Desktop
the terminal emulator is a program that lets us to run the command like inside graphical desktop(with windows, icons, mouse, etc.). So, when we use our desktop, it is not the program but a CUI version which out app enables. it is n't the app ok. We are just using the tool(the terminal) that behaves like the old school full screen text interface. It is called emulator because it emulates a real hardware terminal like the old days when computers had only screens. The common terminal emulator programs are `gnome-terminals`, `xterm`, `konsole` or `terminator`. They can run bash, zsh, or any shell,
###### Some basic utilities
`cat`, `head`, `tail`, `man`. So most input lines entered at the shell prompt have three basic elements 
- Command, it means the programs or utility i want to run like `ls` which list files and directories
- Options, it modifies the behavior of the command, It usually starts with single dash `-` or double dash `--`. Like `-l -a --help --version`. like `ls -l `, `rm --recursive`.
- Arguments, the target or input the command lines works on - files, directories or other data. `ls -l /home/bibek`(`ls` is command, `-l` is option, `/home/bibek` is argument), `cp file.txt /home/bibek/Documents`(`file.tx`, `/home/bibek/Documents` are arguments)
Like this format `command [options] [arguments]`. But some commands can work with just command `clear`, with arguments only `cat myfile.txt` and with options only also `date --utc`.
###### sudo - `superuser do`
It lets regular users temporarily acts like the root user(the all powerful administrator of the system) - just for one command at a time. Instead of switching to the root account completely(like `su`), `sudo` let us by our self but get the power of admin when needed. Most of the time Linux users work as a normal user(not root one) for safety. But we need admin or elevated power and privileges when we want to install software, change system files or modify other user's data. when we type command `sudo`, it asks for our password not the root(it is the confusion but you can feel this once you use it). If you are allowed to use `sudo` it runs the command with root privileges. 

To use sudo, a few things must be in places
- Our use must be in `sudo` group(or `wheel` on some distros).
- The system uses the `/etc/sudoers` file to check who is allowed to run what.
- The file should be edited with care using `visudo` to prevent breaking `sudo`.
The thing is in most distros, `sudo` is already set up during installation. On Other systems like Arch, CentOS, we may need to install `sudo`, add the user to `sudo` or `wheel` group and use `visudo` to configure access. 

###### Setting up and Running `sudo`
Some Linux systems like (like Arch, CentOS minimal, or custom installs) don't enable `sudo` by default. if we `sudo` on such systems, we'll get an error like. `sudo: command not found` or other `user aren't in sudoers file. This incident will be reported`. To not get this we need to manually set up `sudo` access for the user - but to do that, we need to already be the root user, which is where `su` comes in. There are process, you need to search it up at the time of setting it up. 

To enable sudo manually, become root user using `su`. add the config line in `/etc/sudoers.d/bibekkoirala`. and set secure permissions. 

###### Switching between GUI and Command Line.
Linux is very modular and customizable, which means we can start the system in GUI mode(graphical desktop) or start it in command-line mode only(no desktop) and we can switch between the two anytime - manually or automatically. In Personal Laptop, Gaming or Multimedia, GUI is preferred and while programming or DevOps taks, Lightweight server(e.g. VPS)  or Headless server(no monitor), CLI is preferred. 

Most Linux distributions give an option during installation (or have more than one version of the install media) to choose between a desktop or workstation  (with a graphical user interface) or server (usually without one).

We said that we can switch anytime but is is only possible if Both are installed. 
To switch from GUI to CLI(temporarily)
`Ctrl + Alt + F3(or F1 to F6)`. We can go to full-screen virtual terminal and we can use after passing login prompt. We can return to GUI by pressing. `Ctrl + Alt + F1 (or F7 depending on distro)`. This is like jumping to a terminal screen without shutting down the desktop. 

We can also change default startup mode also. 

###### Virtual Terminals
VTs are full screen text consoles that run outside the graphical desktop(like GNOME or KDE). They're part of the core Linux system - available even when our GUI crashes or isn't installed at all. They are called virtual because they share the same physical screen and keyboard but we can switch between them - like tabs in browser, but for whole system display. 

==VT is not the same as the terminal app(like GNOME Terminal or Konsole )==. Terminal app = window inside the GUI while VT = System level console outside the GUI. Only one VT is visible at a time but multiple are active in the background. They are useful for recovery, troubleshooting and low-level tasks. 

We can switch between VTs using 
```
Ctrl + Alt + F1 -> VT1
			 F2 -> VT2
			 F6 -> VT6
```
If already inside a VT, then only Alt + Fn is needed.

So which Vt is the GUI ? 
The Graphical Desktop usually runs on VT1, VT7 or sometimes in VT2(depending on distro). Other VTs(VT2 to Vt6) are for text logins. So if your GUI if frozen or buggy. Press `Ctrl + alt + F3` to open a fresh terminal and login and run troubleshooting commands like `top`, `kill` or `sudo reboot`.

Like if GUI freezes or crashed, switch to VT and fix it from there. If you're on a minimal system(like a server). You're already using a VT full time. ==If you're trying to learn CLI Linux deeply. Practice using VTs instead of GUI terminals==
And every VT login is separate. If you're logged into VT2, someone can still log in on VT3. Logging out of a VT ends that session, but others remain unaffected
![[Pasted image 20250615155548.png]]
###### Turning off the Graphical Desktop
Sometimes we may want to stop the GUI in Linux to save system resources, Troubleshoot a broken laptop, Run in CLI only like in server or many other things. Linux lets us to do this with single commands - so no need to uninstall or reboot at all. Modern Linux distros uses `systemd`, which controls background services like display manager(the part that launches our graphical desktop, eg. GNOME or KDE). We can do that by 
```
sudo systemctl stop gdm
```
This stops the `gdm` and drops to a text-based login prompt(Virtual Terminal). Replace `gdm` with `lightdm`, `sddm`, or `xdm` depending on the distros. 
To turn GUI back on: `sudo systemctl start gdm`. There was old  style methods also and we can use that still in many systems. 
```
sudo telint 3 // switches to CLI(runlevel 3)
sudo telint 5 // switches to GUI(runlevel 5)

// but they are same as 
sudo systemctl isolate multi-user.target   # CLI
sudo systemctl isolate graphical.target    # GUI
```
So basically, Display manager is just another Linux service. We can pause and restart it just like web server or database which makes Linux flexible- not needed to reboot or reinstall to switch modes. 
Stopping the GUI doesn't shut down the system, it just removes the graphical layer - the system is still fully running underneath. 

#### Basic Operations
Logging in start a session whether it is local computer or remove server. So we know there are two main login environment right ? 
1. Text Terminal(Console Login) - full screen terminal with login: prompt
2. GUI Terminal - login screen with username and password fields. 

When we are at VT or logged into a headless server, we'll see
```
login: 
```
Then we can type username or password and password even shown at all. After successfull login , we'll see
```
student@hostname: ~$
```

Also in GUI, we'll see a nice login screen. This is handled bu Display Manager like `gdm`, `sddm`, etc. 

Now the interesting part.
Once we are logged into our own system, we can also log into other systems remote using SSH(Secure Shell).
```
ssh student@remote-server.com
```
`ssh` is command.
`student` is the remote username
`remote-server.com` is machine we're trying to connect to 

SSH encrypts the connection and let us securely access a remote terminal. The way to log in in SSH are;
- using a password for login(just like local)
- or use a key file to log in without a password, which is more secure

To end session `exit`.

###### Rebooting and Shutting Down
For this `shutdown` command is used. This send the waning and prevent further uses from logging in. The `init` process will then control shutting down or rebooting the system.   
```
sudo shutdonw -h now // -h means halt
sudo shutdown -r +5(in minutes)
```
It send system-wide warning to logged-in users
Blocks new users from logging in
and passes control to `init` or `systemd`
Since shutting down affects all users and services, it requires `sudo` privileges. 

###### Locating Applications
Linux allows flexibillity and seperation of system-level, user-level and optional software, programs can be located in multiple directories depending on
- Who installed it(you or the system)
- How it was installed(package manager, script, compiled manually)
- The system's policy(varies between distros)

`/bin` has essential user commands. `/usr/bin` has most user installed programs, `/sbin`, `/usr/sbin` has system or admin commands like `iptables`, `shutdown`. `/usr/local/bin` has locally compiled or manually installed programs. `/opt/` optional software(usually third-party apps). `/home/username/bin` has personal scripts or binaries for that specific user. 

We can find where a program is installed

`which` finds the first match in our path. `which diff` shows the executable path and searches only what is listed in our `PATH` environment variables. 

`whereis` - finds binaries, source code and man pages. 
```
whereis diff
```
much broader search. 

`command -v` - Shell built in version of `which`(more portable)
```
command -v diff
```

`type` - tell you if it's a command, alias or function
```
type diff
```
this is useful when you're not sure if something is a real file or an alias. 

And what is `PATH` ? 
`$PATH` variable tells Linux where to look when a command is typed like `code`, `python` or `npm`.
like `echo $PATH` and we get the folders that Linux searches in order to find the command. We can also add our own folders(like `~bin`) if we want to run custom scripts from anywhere. 
```
export PATH="$HOME/bin:$PATH"
```

Here is the path

![[Pasted image 20250615173258.png]]
###### Hard and Soft(Symbolic) Links
==Remember there is difference between Hard Link, Copy and Soft Links==.
Hard Link just means giving existing file another name. Like giving someone a second name, it is still the same file, just accessible via two names. In Linux, every file is tied to a inode - a unique data structure that stores everything about the file except its name(like size, permissions, location on disk etc.).

Like with `ln file1 file2` creates a file `file2` a hard link to exact same inode as `file1`. So now both are just name point to same file content. What if we delete one ? we just deleted the name that is it. If all the names are deleted, then Linux deletes the actual file content. To understand the hard link you can see inode number by searching in AI. 

However there is some things to remember when editing
Say you open `file2` in `vim` or `nano` and save it — you’re still editing the **same inode**, so changes appear in both `file1` and `file2`. _But watch out!_ Some GUI editors (like `gedit`) rewrite files instead of modifying them in place. This might break the hard link by creating a new inode. So `file1` and `file2` would become separate at that point.

About Soft Links
A Symbolic Link(or symlink(think of it as reference not data)) is like a shortcut - a reference that points to another file or folder. You click the shortcut, and it leads you to actual target. Think of it just like a pointer. It is not a file itself.just a desktop shortcut to a file buried deep in folders. 
```
ls -s file1 file3
```
This creates a symbolic link named `file3` that points to `file1`. Here we can see `file3` has different inode from `file1`. It looks like `file3 -> file1` clearly showing that it points to `file1`.It uses very little space. It also breaks if target is deleted. 

Unlike hard links, soft links can point to object even on different file systems, partitions, and/or disks and other media which may or mayn't be currently available or even exist. In the case where the link does not point to a currently available or existing object, you obtain a dangling link.In end, soft link is just pointer. 

About Dangling Symlink
A dangling link is a symlink that points to a file and directory that doesn't exist(may be it was deleted or isn't mounted yet).

While Hard Link doesn't work under partitions but soft links does. 
#### Working with files
Prompt String 1 `PS1` is shell variable in bash(and many other shells like `zsh`). It is the part of Bash shell and not Linux. It is supported by other shells also. It is just a way to make prompt dynamic. Without `PS1`, the shell would still work, but we might not see prompt at all or very minimal one like `$`.

Like "Hey shell, print this text before waiting for input. "
#### Searching for Files
###### Understanding the file descriptors.
When a command is executed in Linux, three **standard file streams** are automatically opened:
The name of Stream in Standard Input(stdin). the file descriptor is 0 and Default Source or Destination is Keyboard. Another is Standard Output(stdout), the descriptor is 1. The Default Source or Destination is Terminal. And name of another stream is Standard Error (stderr). The file descriptor is 2 and Default source/Destinaion is Terminal(seperate from stdout). So these are the default communication channels between the user and the command-line processes. 

So basically in Linux, Every command takes input, gives output and may show errors. Linux gives 3 special "channels"(called streams). so basically `stdin` takes your input and has number 0 and same for `stdout` and `stderr`.  so 0 means input, 1 means output and 2 means error. 
Internally, Linux uses file descriptors(FDs) to represent all open files. ![[Pasted image 20250619194057.png]]
so, typically if other files are opened in addition to these three, which are opened by default, they will start at file descriptor 3 and increase from there. 

###### I/O Redirection
I/O Redirection just means changing where a command get its input or sends its output. So by default Input(`stdin`) comes from the keyboard. Output(`stdout`) and errors(`stderr`) go to the terminal screen. With redirection, we can read input from a file, write output or errors to a file and chain commands together. So in easy words, rather than writing the command in keyboard, we take from some file or whatnot and instead of getting the output from screen, we can get it printed to `output.txt`. we can easily append or override the output. 

```command
	do_something < input.txt >> output.txt
	cat < input.txt > ouput.txt
```
Here input comes from the file and output gets appended to the end of `output.txt` rather than replacing it. 
In second command it overwrite `output.txt` rather than append it. And cat read from `input.txt`.

###### Pipes
Pipe means `|`. It let us connect multiple commands, so the output of one becomes the input of the next . 
```
	command1 | command2 | command3
```
Simple, this is pipeline. Instead of saving o/p to something and reading it back in next step, pipes connect commands directly in memory. Basically, Linux follows the "Do one thing well" mantra. Have small, simple commands that do specific tasks. Combine them to do complex things. Basically we can generate workflows with it. In multi-core CPU. All commands in the pipeline can run at the same time - super efficient. It is like chaining Logic.
![[Pasted image 20250619200006.png]]
###### Searching with files
`find` searches in real-time and is slow and accurate
`locate` searches a pre-built file index(database) and is very fast.
`wildcards` match file patterns in shell and is instant for current folders. 
```
	find [path] [options]
	find . -name "notes.txt" // -name means search for exact match, . means start from current directory
```
and you can use for other commands also.
###### locate
###### Wildcards and Matching File Names
Wildcards just means special character. We can use them with `ls cp mv rm`, etc. There are two main wildcards `*` and `?`, Basically, `*` matches zero or more characters and `?` matches exactly one character. Like `*.txt` means all files ending with `.txt`.
![[Pasted image 20250619201320.png]]
###### find
It walks through the file system tree, starting from a specified path(or from current directory). It can search based on file name, size, time of last access or last modification, file type, permissions and more. Unlike `locate`, `find` works in real-time, and doesn't rely on pre-built database. 
```
	find [path] [options] [expressions] // default path is . 
```
###### Advance find options. 
###### Finding files based on time and size
#### Installing Software
The core parts of Linux distribution and most of its add-on software are installed via the Package management system. Each package contains the files and other instruction needed to make one software component work well and co-operate with the other components that comprise the entire system. 

The package is a compressed file that contains the actual software binaries or scripts, metadata(name, version , description) and dependency information(what other package it needs to run). Like a python web app might require package like `python3`, `flask`, etc. 
Some handle dependencies automatically - use `apt` or `dnf` for that
###### Package Managers: Two levels
We have two package management systems. Low level and High level package. High level tools calls low-level behind the scenes. Always be cautious as High level packages may install hundreds of dependencies.  ![[Pasted image 20250619204049.png]]
###### Working with different Package Management systems
