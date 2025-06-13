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

## Linux Basis and System Startup
#### The boot process
###### The first step
It is the procedure for initializing the system. Like when user  press the power button and to the position when UI is functional. 
![[Pasted image 20250608180529.png]]
![[Pasted image 20250608180554.png]]
While linux runs on many kinds of hardware, we will concentrate on x86 family, which is most of the computers. Starting an Linux in x86 system involves a number of steps. When the computer is powered on, The OS isn't the first thing that wakes up. There are some steps that must happens first, like checking and warming up if everything is okay before starting to work. The process is called booting.  It is actually BIOS that wakes up at first and not OS. Think of it as a computer's first bran that wakes up. It is stored in the tiny software permanently on a special chip on the motherboard. So firs BIOS initializes hardware like checking screen, keyboard, and memory, RAM. It checks if those parts are working or not. It is called POST - Power-On-Self Test. If everything is working fine. It moves to finding the OS(in our case Linux). BIOS doesn't exists in hard drive. It can't be erased by normal means. It is on special chip(ROM) that can't be erased by normal means. And that chip exists in the motherboard. So, after check is finished, BIOS, hands over the control to boot able devices(like hard drive or USB or CD/DVD) and OS can start loading. BIOS picks the first bootable device based on the boot order settings. 
![[Pasted image 20250608181020.png]]
The BIOS software is stored on a read-only memory chip on the motherboard. After this, the remainder of the boot process is controlled by the OS.
###### Master Boot Record(MBR), EFI Partition, and Boot Loader
So POST becomes completed and system control passes from the BIOS to the boot leader. The boot loader is small program that is stored on a storage device like a hard drive or SSD, either in the boot sector(for traditional/ older BIOS/MBR systems) or the EFI partition(for newer UEFI systems). Up to this stage, the machine doesn't access any mass storage media. Then, information on the date, time and most important peripherals are loaded from the special memory called  CMOS. This memory is powered by a small batter, so it can keep track of date and time even when the computer is turned off.  

A number of boot loaders exist for Linux. The common ones are 
- GRUB (for Grand Unified Boot Loader)
- ISOLINUX(for booting from removable media),
- DAS U-BOOT(for booting on embedded devices/appliances)
Most Linux boot loaders can present a UI for choosing alternative options for booting Linux and even other OS that might be installed. When booting Linux, the boot loader is responsible for loading the kernel image(the core part of Linux) and initial RAM disk or filesystem(which contains some critical files and device drivers needed to start the system) into memory. It loads these into the computer's memory so Linux can begin running. 
![[Pasted image 20250610201934.png]]
###### Boot Loader in Action
BL has two distinct stages which depends on system types:
1. ==For Older Computer(BIOS/MBR)==: The boot loader start in a very small space called Master Boot Record(MBR)(It is first sector of the hard disk), which is the first 512 bytes of the hard disk. In this stage, the boot loader examines the partition table examines the partition table and finds a bootable partition. Once it finds a bootable partition, it loads the second stage boot loader(like GRUB) in to the computer's memory(RAM) and continue the booting process. In easy words, the boot loader lives in a tiny part of the hard drive(called MBR). That tiny boot loader looks around to search where the main part of the system is. Then it brings in the bigger boot loader(like GRUB) to get Linux started. This is for Older computer. 
2. ==For Newer Computers(EFI/UEFI systems) ==: Let's say in easy words. The computer's built-in system (called UEFI) already knows where to find the boot loader. It looks up the info and launches the boot loader directly from right place. This way is a little bit more complicated but better and more flexible. In little bit technical terms. The UEFI firmware has a built in Boot manager that knows where boot loaders(like GRUB) are stored. It read this info to find the right disk and partition with the EFI partition(a special place where boot loaders like). The firmware then starts the boot loader program defined in its settings. It is more complex but more flexible than MBR method.

For the second stage Boot loader
The second stage boot loader lives in special folder called `/boot` on host's computer. When it runs, it usually shows a simple menu or splash screen where you can pick which OS and/or Linux version(kernel) you want to start.
After the choice, the boot loader loads the Linux kernel into the computer memory(RAM)(so controls goes here). The kernel is usually  always compressed(like a zipped file), so the first things it does is unzip itself to get ready. Then kernel checks the hardware(like keyboard, screen and memory). It also turns on the built-in device driver(small programs that helps the kernel talk to hardware).
###### Initial Ram Disk
Now, when Linux is starting up, it uses a temporary mini-filesystem in memory called initramfs. This is like small toolbox that helps the system get ready before the real system starts. 

What is inside initramfs ? 
It has programs and files that helps the system find and mount the real root filesystem(where linux actually lives).
It also includes drivers(small programs) needed to talk to your hard drive or SSD.(It means if our computer needs special driver to talk to our hard drive, those drivers must be inside initramfs - otherwise , Linux won't be able to find your files) It also uses something called `udev` which looks at what hardware is connected(like hard drive). finds out what drivers are needed and loads those drivers so Linux can work with them. 

What happens next?
After finding real root filesystem, Linux checks it for any errors. Then it mounts it - this means it makes the filesystem ready to use and attaches it to the Linux file structure(like plugging in a USB and make it appear in the folders). Once the real root file system is ready, the initramfs is removed from memory. Then, Linux run the init program(found at `/sbin/init`), which takes over the rest of the startup. 
![[Pasted image 20250610205730.png]]
Once the Linux kernel finished loading and real root filesystem is ready, it runs a program called `init`. This is the first real program of the OS. you can think of `init` as the boos that starts everything else in Linux system.
Like it 
- Set up system environment means it gets things ready so we can use computer smoothly like setting the time, network, etc. 
- Starts background services like bluetooth, Wi-Fi, sound, etc.
- Start our login screen. If we are using a desktop, `init` will launch the graphic login screen(or terminal login if we are using command line).
Note, for older linux systems, the program was literally called `init`. In modern Linux systems, it is usually replaced by newer system called `systemd`, but it still does the same kind of job - starting and managing everything after boot 
###### Text Mode Login or Graphical Login
Near the end of boot process, `init` starts a number of text-model login prompts. If system uses a graphical login, you'll see a login screen where you enter username and password. I your system your text-mode login(no GUI), you'll see plain black screen login prompts(on terminals). You can switch between these terminals with keys like `Ctrl + Alt + f1 to f6`. You login in by typing your credentials and then get access to the command shell(a place to type Linux commands).
![[Pasted image 20250610211900.png]]
So basically After the kernel loads, `init` or `systemd` run first, It starts all the services your system needs. It launches the login screen(graphical or text). Then only we get to use our Linux system. 

And as you login through text-mode screen, you  get something called `command shell`. The most common shell is `bash` (Bourne Again Shell). But there are other shells too like `zsh`, `fish`, etc with their own features. When the shell is ready, it shows a text prompt(something like `$` or `username@computer:~$`). 

###### Revision
As soon as Power is On, BIOS/UEFI wakes up the hardware. It check everything is working or not. This is called POST. After that BIOS looks for a bootable devices to find the system to load. 

Bootloader starts(GRUB)
Then BIOS/UEFI hands control to the bootloader - usually GRUB. It lives in special place on the disk(MBR for BIOS, EFI partition for UEFI). GRUB shows a menu(splash screen) so we can choose which OS or kernel version to boot.

Kernel loads. 
After selection or it auto-selects, GRUB loads the linux kernel(the core of the OS) into RAM. The kernel is usually compressed, so it unzips itself first. The it checks the hardware and gets ready. 

Initial RAM Disk
Along with kernel, a special mini file system called initramfs is loaded. This contains tools and driver to find real root file system, load storage devices and detect connected hardware using udev. Once real Linux system is foundded and mounted, initramfs is deleted from RAM. 

Now Init or systemd takes over
Now first real program starts: `init` or `systemd` starts. It is the boss who start everything else. It set ups time, network, etc. starts background services, shows you login screen - graphical or text-based. 

Login Screen(GUI or text mode)
Now you can see GUI Login screen(if using a desktop) or a text-mode login prompt(for CLI only systems or servers). you type your username and password to log in 

Shell Starts
Once logged in, it loads your desktop if it is GUI and loads bash if it is GUI. 

Power On → BIOS/UEFI → Boot Loader (GRUB) → Kernel → Initramfs → systemd/init → Login screen → Shell
#### Kernel, init and Services
###### Linux Kernel
The bootloader loads the kernel and initial RAM-based file systems (initramfs) into memory, so it can be used directly by the kernel. 

When the kernel is loaded in RAM, it immediately intialized and configures the computer's memory and also configures all the hardware attached to the system. This includes all processors, I/O subsystems, storage devices, etc. The kernel also loads some necessary user space applications. 

![[Pasted image 20250610223154.png]]
###### `/sbin/init` and Services
So once kernel finishes its job, which includes sets up the hardware and mounts the root filesystem(the main disk structure). It runs a program called `/sbin/init`. Like we already know `init` is first user-space program the kernel starts. Like is process #1(the parent of all other user-space processes). Like a main organizer of entire linux system after kernel. So init starts the whole system(like launching all the background services, start the graphical interface so we can use the system). Keeps the system running and something crashed, `init` can restart it. It also clean up and manages process. Like when other program finish or crash, `init` handles their cleanup. This helps prevent `zombie` processes. And it also shut the system properly. But kernel processes. Not everything comes from `init`. Some special programs are started directly by the kernel - these are called kernel processes. They take care of low-level OS tasks(like memory and CPU management) and don't show up in regular user process list. 

So there is difference between how linux starts services in old and new methods. in 1980s, Linux(like UNIX) used a method called SysVinit. It started the system in a step-by-step(serial) way. In old system, it used run levels - these are like "models" of operation. Like one example, Run level 3 = multi - user mode with no GUI. Run level 5 = multi-user mode with GUI. Run level 0 = shutdown. Each run level ran a set of scripts to start or stop services(e.g. networking, printing, login). But it was slow, because everything had to wait for previous service to finish before starting the next one. 

However new way - systemd
Modern linux systemd - a faster and smarter system initializer. It starts services in parallel (simultaneously), which is much faster. It also tracks dependencies between services("start networking before web server"). Manager the entire life cycle of services: start, stop, restart, monitor, log and more. 

Legacy method is still default in modern distros also. 
###### Startup Alternatives
**SysVinit** viewed things as a serial process, divided into a series of sequential stages. Each stage required completion before the next could proceed. Thus, startup did not easily take advantage of the **_parallel processing_** that could be done with the multiple processors or cores found on modern systems.

Furthermore,  starting up and rebooting were seen as relatively rare events; exactly how long they took was not considered important. This is no longer true, especially with mobile devices and embedded Linux systems. Some modern methods, such as the use of containers, can require almost instantaneous startup times. Thus, systems now require methods with faster and enhanced capabilities. Finally, the older methods required rather complicated startup scripts, which were difficult to keep universal across distribution versions, kernel versions, architectures, and types of systems. The two main alternatives developed were:

**Upstart**
- Developed by Ubuntu and first included in 2006
- Adopted in Fedora 9 (in 2008) and in RHEL 6 and its clones

**systemd**
- Adopted by Fedora first (in 2011)
- Adopted by RHEL 7 and SUSE 
- Replaced Upstart in Ubuntu 16.04

While the migration to **systemd** was rather controversial, it has been adopted by all major distributions, and so we will not discuss the older System V method or Upstart, which has become a dead end. Regardless of how one feels about the controversies or the technical methods of **systemd**, almost universal adoption has made learning how to work on Linux systems simpler, as there are fewer differences among distributions. We enumerate **systemd** features next.
###### features of systemd
Systems with **systemd** start up faster than those with earlier **init** methods. This is largely because it replaces a serialized set of steps with aggressive parallelization techniques, which permits multiple services to be initiated simultaneously.

Complicated startup shell scripts are replaced with simpler configuration files, which enumerate what has to be done before a service is started, how to execute service startup, and what conditions the service should indicate have been accomplished when startup is finished. One thing to note is that **/sbin/init** now just points to **/lib/systemd/systemd**; i.e. **systemd** takes over the **init** process.

One **systemd** command (**systemctl**) is used for most basic tasks. While we have not yet talked about working at the command line, here is a brief listing of its use:

- Starting, stopping, restarting a service (using **httpd**, the Apache web server, as an example) on a currently running system:  
    **$ sudo systemctl start|stop|restart httpd.service**
- Enabling or disabling a system service from starting up at system boot:  
    **$ sudo systemctl enable|disable httpd.service**
- Checking on the status of a service:  
    **$ sudo systemctl status httpd.service**

In most cases, the **.service** can be omitted. There are many technical differences with older methods that lie beyond the scope of our discussion.

The Apache is web server. The job of Apache Web Server is to server html files. Let's say we type `example.com`. Behind the scenes, request goes to web server. The server receives that request, finds the correct files(like an HTML page), and sends it back to browser that is it. If we install apache, it let use host a local website on our computer. You can visit localhost and it will show. There are other web servers also. Like Apache, Nginx, Node.js, Express etc. Infact, web server is anything that listen on a port, accepts HTTP requests, returns a response. So the questions may arises "Why did my website worked without installing apache or nginx" ? but when you make node.js, live server or vercel/render. they are initializing web server in their own way. in node.js our js code makes web server, in liver server mini-server is made for static files. In vercel, they set up and run the server for us. But it is absolutely necessary for use to learn ==Apache web server(httpd or apache2) and Nginx  ==
Normally Dev Ops setup web server. Production ma manually hunxa hai set up garne kura haru. jastai port, server config, domains. In production port 80, 443 is used. in VPS also browser doesn't care as long as something is answered for it http request.
#### Linux file system
filesystem is how your computer stores your files, knows where they are, lets you retrieve them quickly. Linux supports different kinds of file systems, based on how and where data is stored. 

Disk filesystem for regular hard drivers & SSDs. 
- `ext3` and `ext4` it is mostly available for Linux.
- `XFS`, `Btrfs` and `JFS`. Powerful alternatives, used in enterprise setups. 
- `NTFS` - Windows file systems.
- `vfat`, `exfat` - Used in USB drives, memory cards, etc. 

Disk for flash storage filesystem - for USBs or embedded devices
- `ubifs`, `jffs2` and `yaffs`- designed to be super fast and durable for small storage devices. 

Disk for Database Filesystems
Store files kind of like a database, useful for advanced cases. 

Disk for Special Purpose Filesystems
These don't store user files - instead, they help the OS run properly
- `procfs` - lets you read system info(like `/proc/cpuinfo`)
- `sysfs` - shows hardware info(like `/sys`)
- `tmpfs` - stores temporary files in RAM (very fast)
- `squashfs` - Compressed read-only storage (e.g, Live USB)
- `debufs` - Helps debug the file system
- `fuse` - let users create filesystem in user space. 
Almost all Linux systems use a common layout, just like all libraries have front desk, fiction section, reference section, etc. 
- `/bin` - Basic system commands(like `ls`, `cp`)
- `/etc` - Configuration files
- `/home` - Your personal files(`/home/you/`)
- `/var` - Logs, spools, cache
- `/tmp` - Temporary files
- `/user` - User programs and libraries
- `/root` - Home directory for root user
- `/boot` - Boot loader files, kernel
- `/dev` - Device files (USB, disk, etc.)
- `/proc` - System info from the kernel
- `/sys` - Hardware and device info
- `/mnt media` - Mounted drives like USBs.
![[Pasted image 20250611212605.png]]
A file system is jsut a method of storing and accessing files. and parititon can be thought as a container in which a filesystem resides. However in some circumstances, a filesystem can span more than one parition if one uses symbolic links. 

###### File system hierarchy Standard
Linux store their important files according to standard layout called File system Hierarchy Standard(FHS). You can go to link to discuss better `https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.pdf`.
Linux uses `/` unlike windows  (`\\`) and doesn't have drive letters. Instead everything is part of one big tree of folders. so even if you plug in a USB or have multiple drives/partitions, they get "mounted" somewhere inside this tree.

The big things is where do USB and DVDs show up ? 
In newer system it shows up in `/run/media/yourusername/LabelOfDrive` and in older Linux systems, it might appear in `/media/LabelOfDrive`. Linux file system names are case-sensitive. However in windows `C:\File.txt` is same as `C:\file.txt` but not in linux.

There is big role of `/usr` in linux. In linux, essential system files(needed to boot and run the system) is found directly under `/` like `/bin`, `/sbin`, `/etc`, `/lib`, `/boot`. Not essential programs and libraries for users are found under `/usr`. So, `/usr` is like "second root" for user-related files and programs. 
`/usr` might have `/usr/bin`, `/usr/sbin`, `/usr/lib`, `/usr/share`, `/usr/local`

![[Pasted image 20250611213453.png]]
#### Linux Distribution Installation
Many embedded system uses custom crafted content rather than Android or Yocto.

![[Pasted image 20250610224943.png]]
###### Questions to ask when you want to install linux
- What is the main function of the system (server or desktop)?
- What types of packages are important to the organization? For example, web server, word processing, etc.
- How much storage space is required, and how much is available? For example, when installing Linux on an embedded device, space is usually constrained.
- How often are packages updated?
- How long is the support cycle for each release? For example, LTS releases have long-term support.
- Do you need kernel customization from the vendor or a third party?
- What hardware are you running on? For example, it might be **X86**, **RISC-V**, **ARM**, **PPC**, etc.
- Do you need long-term stability? Or can you accept (or need) a more volatile cutting-edge system running the latest software versions?


## Graphical Interface
