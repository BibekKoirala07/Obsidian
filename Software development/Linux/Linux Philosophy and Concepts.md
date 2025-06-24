## Linux Philosophy and Concepts
Linux releases kernel every 3 months. Linux is just operating system. At is core, it is just a kernel. At 1991, Linux Torvalds created Linux Kernel(the core of Operating System) . Later it was given a special free license(called the GPL) by GNU project which made is easy and legal for lots of people around the world to help improve it. So by combining Linux with other free software from GNU, many developers made full operating system called Linux distribution. 

But remember that just kernel doesn't make operating system. Kernel is the one that talks directly to the computer hardware. It manages resources, runs programs, handles files and controls everything at the lowest level. We also need System libraries and tools(to interact with kernel easily), user programs(like file managers, text editors, web browsers), shells and command line tools(to type commands), Graphical interface(optional, but to see windows and icons) and also different utilities for networking, printing and other hardware support tools also and then it becomes full Operating System. For linux, it comes from GNU project. 

Linux borrows heavily from the well-established family of UNIX operating system. like files are stored in a hierarchical file system with top node of the system being the **root** or simple "/". Whenever possible, Linux makes it components available via files or objects that look like files. things like processes, devices and network sockets are all represented by file like objects and can often be worked with using the same utilities used for regular files. Linux is a fully multitasking( multiple threads of execution are performed simultaneously), multi user operating system with built-in networking and service processes known as daemons in the UNIX world. 


> [!NOTE] Thing to remember
> Linux isn't UNIX. It is inspired by UNIX

#### Linux Terminologies
###### Kernel
It is the brain of Linux OS. It controls the hardware and makes the hardware interact with the applications. An example is Linux Kernel. 
###### Distributions
Collections of programs combines with Linux kernel to make up a Linux OS. Some common distributions are RHEL, Fedora, Ubuntu.
###### Boot Loader
The program that boots the operating system. The examples of boot loaders are GRUB and ISOLINUX.
![[Pasted image 20250608171750.png]]
###### Service
The program that runs as a background process. Some examples are httpd, nfsd, ntpd, ftpd and named.
![[Pasted image 20250608171816.png]]

###### File System
The file system is a method for storing and organizing files in Linux. Some examples of file systems are ext3, ext4, FAT, XFS and Btrs.
![[Pasted image 20250608171829.png]]
###### X Window system
The X window system provides the standard toolkit and protocol to build graphical user interfaces on nearly all Linux systems.
![[Pasted image 20250608171854.png]]

###### Desktop environment
The DE is a graphical user interface on top of the operating system. e.g.  GNOME, KDE, Fluxbox
###### command line
The command line is an interface for typing commands on top of the OS. 
###### shell
The Shell is the command line interpreter that interprets the command line input and instructs the OS to perform necessary tasks. For e.g. bash, tsch, zsh, e.t.c.

> [!NOTE] When we make the program in linux, we need to make sure it is supported by most of the distros
> For that we need to learn about different components, services and configurations associated with each distribution.

#### What does Linux OS contains ? 
![[Pasted image 20250608173352.png]]
Obviously, it contains Kernel, plus it contains 
- package updates, upgrades kernel and driver patches. Out distros provides regular updates for security and performance
- applications. Text editors, browsers, terminal tools, etc. normally distros bundle common applications you'd need out of the box. 
- libraries, utilities, configuration. like libraries means reusable code apps need(e.g. libc). utilities means essential tools like `ls cd cp`. and configurations files for customizing system.
- documentations. help for using commands, application and services. like `man` commands
- support services. like help from the community or may be commercial support. 

#### Services and Support
For this, we have two types, community and commercial services. Like RHEL, SLES, Oracle have commercial services. While CentOS, OpenSUSE, etc. have community services.
