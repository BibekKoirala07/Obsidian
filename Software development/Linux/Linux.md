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
A file system is just a method of storing and accessing files. and partition can be thought as a container in which a files system resides. However in some circumstances, a file system can span more than one partition if one uses symbolic links. 

###### File system hierarchy Standard
Linux store their important files according to standard layout called File system Hierarchy Standard(FHS). You can go to link to discuss better `https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.pdf`.
Linux uses `/` unlike windows  (`\\`) and doesn't have drive letters. Instead everything is part of one big tree of folders. so even if you plug in a USB or have multiple drives/partitions, they get "mounted" somewhere inside this tree.

The big things is where do USB and DVDs show up ? 
In newer system it shows up in `/run/media/yourusername/LabelOfDrive` and in older Linux systems, it might appear in `/media/LabelOfDrive`. Linux file system names are case-sensitive. However in windows `C:\File.txt` is same as `C:\file.txt` but not in linux.

There is big role of `/usr` in linux. In linux, essential system files(needed to boot and run the system) is found directly under `/` like `/bin`, `/sbin`, `/etc`, `/lib`, `/boot`. Not essential programs and libraries for users are found under `/usr`. So, `/usr` is like "second root" for user-related files and programs. 
`/usr` might have `/usr/bin`, `/usr/sbin`, `/usr/lib`, `/usr/share`, `/usr/local`

![[Pasted image 20250611213453.png]]
## Linux Distribution Installation
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

###### Linux Installation: Planning
It is best to plan out partition layer layout when installing Linux. Changing it later can be tricky. Linux allows you to use multiple partitions(space used for specific purposes like system files, personal files, swap space, etc. ) by mounting them at different places in the system, and while you can change the setup later, it's much easier to plan it properly from start. Linux mounts partition to folders like `/`, `/home`, `/boot`, etc. This lets the system use multiple partitions as if they are part of one big structure. 
![[Pasted image 20250613151054.png]]
most Linux installers create a default partition layout that works well for general use. Usually they do these things
- Put everything(all your files and system data) in one big partition(like `/`) and create a small swap partition(used like extra RAM). 
- Or they may create separate partitions for important folder like `/home`(your personal files) and `var`(system logs and app data), to manage spaces. 
But many times we may want to customize this settings. Like we may have special storage needs e.g. for a server or development environment or sometimes we may be using more than one hard drive or SSD, and want to control what goes where. In short, Linux gives use good default layout, but we can change it during installation if our setup needs more control. 
###### Linux installation: Software Choices
When we install Linux, it always includes the bare essentials needed to get Linux up and running. Linux comes with basic system tools so the OS can run properly. Also for security setup: Like You'll set a password for the main user. Some systems like (Ubuntu) don't let you log in directly as root; instead, you use `sudo` for admin tasks. 

Most Linux installers offer categories of software you can add:
Like Web browsers e.g. Firefox
Office tools: e.g. LibreOffice
Developer tools: e.g., `vi`, `emacs` , compilers
Server tools: e.g. Apache Web server, My SQL (database)
Graphical Desktop: Like GNOME, KDE

There is difference between the software provided in Old Linux and new Linux. In Old Linux, Installation had lots of software choices upfront. In Modern Linux, we can add more software after installation. 

In terms of security. All systems get up some basic security(user accounts, passwords). some include advanced protection systems like SELinux(used in Fedora, Cent OS etc.) and in App Armor(used in Ubuntu).

###### Linux Installation: Install Source
There can be many ways to install Linux.
One is Removable Media(like USB SD DVD). You can put Linux installer on a USB. plug it in, boot your computer from it, and install Linux. 
Another is Network Installation. You can use tiny image to start the installer and it will download the rest from the internet. Some systems even support network booting, so you don't need any USB at all. 
Another is Automatic Installation(Unattented). For a bulk or advanced setups(like servers), you can automate the install using a special configuration file:
- Kickstart file -> used by RH Based Systems
- AutoYast profile -> used by SUSE-based systems
- Preseed file -> used by Debian-based systems.
###### Linux installation: The process
It is similar in nearly all distribution. 
1. Start the installed. Like boot the computer from installation media(like USB). This launches the installer program
2. Answer Setup Questions like how to you want to setup your system - like language, keyboard, disk partitions, user accounts, etc. 
3. Installation happens. The installer copies the system files and setup Linux.
4. Reboot into new system. Your computer restarts as Linux. 
5. May ask some more questions. that is it. 
Most installers let us download and install updates during the installation if connected to the internet. 

We can install Linux directly in our machine. but it erases our data. We can use alternative methods to keep our data. Those alternative methods are :
1. Dual boot, like splitting computer storage into parts so we can use two OS side by side
2. Using Hypervisor to run Linux inside current system. means running Linux as a Virtual Computer(Virtual Machine) inside current OS using special software like VMWare or VirtualBox. So No need to change or partition hard drive.
3. Another one is Booting from Live CD or USB Disk. This let us use Linux directly from USB without changing anything on the computer. It is temporary. Don't do this. It harms USB read and write cycles.

## Graphical Interface
###### X Window System
Loading the graphical desktop is one of the final steps in the boot process of a Linux Desktop. Historically this is known as X Windows System often called X. 
X(X Window system) is the middleman that helps applications show windows and graphics on the screen. or it is classic system Linux uses to show graphical desktops.  TLDR, we can think X as a graphics server for linux computer. Or it is  program what manages how things are drawn on the screen. It lets other program like web browser, file manager show windows, menus, buttons and images. X server run on our computer and controls the display, keyboard and mouse. The program that we run are called X Clients. They ask the X server to draw things on the screen or get input from keyboard and mouse. X server handles the actual screen and input devices. 

Display Manager is a program that runs during startup. It manages the screens and launches the X Server. It also show the login screen where we can enter the username and password. After login, it also start the desktop environment. 

X has been around 1980s, so it's old and has some security and design problems in modern computer. So Wayland start to replace it. Wayland does the same job as X(showing graphics and windows) but in a more modern, secure and efficient way. To users, wayland looks almost exactly like X except with better performance and security under the hood. 


![[Pasted image 20250613161514.png]]
###### More about graphical desktop
Basically desktop environment is basically everything we see and interact in our screen with GUI. It is made up of session manager and window manager. 
We also have session manager. It is the part that start and keeps running all the things we need for desktop sessions like panels, background and other desktop tools. It makes sure everything starts and keeps working smoothly.
We also have window manager. This controls how windows behave - where they appear, how to move, resize, minimize or close them. it also controls thing like window like window title bars and buttons. 

They work together with set of tools(utilities), the session manager and window manager all working as one package. this is the combination that give us a nice smooth, usable desktop experience. 

![[Pasted image 20250613170253.png]]

When the computer boots up, it runs many background processes. One of last process is to start the graphical desktop environment(when we turn on computer and see things like windows, icons, menus, button, etc. that is graphical desktop).

Sometimes we mayn't see those graphical desktop or when we get black screen after booting (may be problem with driver or display manager or we may want to run Linux in server) but we can start it by `startx` and it start graphical desktop.  we can start display manager manually `gdm kdm xdm` from command line which will show the login screen

 TLDR Desktop Environment = Session Manager + Window Manager + utilities. Session manager starts and manages graphical desktop session. Window manager control windows on the screen and Display manager shows the login screen and launches the desktop session. 

###### GNU Startup
When you install a desktop environment, the display manager starts at the end of the boot process. It is responsible for starting the graphics system, logging in the user, and starting the user’s desktop environment. You can often select from a choice of desktop environments when logging in to the system. The default display manager for GNOME is called `gdm`. Another popular display manager is `kdm`, associated with KDE.

###### Desktop Environment
There are many DE like GNOME, KDE, XFCE and LXDE. One of the older one but now unused was Unity(based on GNOME). Gnome Tweaks is actually settings. It just exposes more settings to use that is it. The visual appearance of applications (the buttons, scroll bars, widgets, and other graphical components) are controlled by a **theme**. Normally GNOME has migrated its settings to gnome-tweaks extension.

Users can take turns using the machine, while keeping everyone's sessions alive, or even be logged in simultaneously through the network.

###### File Manager
Each distribution implements the `Nautilus`(File Manager) utility, which is used to navigate the file system. Files/Folders that start with a `.` are hidden. They are typically configurations for the apps, like terminal behavior or GNOME tweaks. example are `.bashrc .zshrc .gitconfig .config`. In Linux we have `/` as root. `/home` is just a folder that contains all user folders. Deleting the file with automatically go to `.local/share/Trash/files`. It is under the user's home directory.

The Home folder is just `/home/bibekkoirala`. It is constant source of confusion. The `Desktop Downloads Videos` that are in file manager are just sub folder that are automatically created in `/home/bibekkoirala/Desktop` and so on. 

## System Configuration
#### System Display, Date and Time Settings
Linux always use Coordinated Universal Time(UTC) for its own internal timekeeping. UTC is similar but more accurate than Greenwich Mean Time(GMT).
#### Package(Software) Management
`dpkg` is the underlying package manager for these systems.It is low-level tools to install or remove `.deb` files
It actually doesn't automatically download package from internet and also doesn't install extra package needed(dependencies). let's say some software might need some dependencies like if you email client need SSL/TLS, then you need to install that SSL/TSL to install email client. just installing email client isn't possible. So people normally use higher level packages like `apt apt-get(older version of apt) synaptic(graphical tool)`. These tools download package, handle dependencies and update software. Like `sudo apt insall firefox` will download firefox and everything it needs. 
![[Pasted image 20250613210926.png]]
We also have `gnome-software(app-store like GUI)`. We also have Ubuntu Software Center(Ubuntu own graphical interface). Even though they look different, all of these use `apt` in the background. apt get software from repo - online storage location full of `.deb` packages. Ubuntu, Debian, Pop OS all have their own repo. And even though these all use apt and `.deb` files, the software versions and configurations inside can be different, so they're not always compatible. And most repo target a particular distribution like Ubuntu and software distributors ship with multiple repo to support multiple distributions. 

RPM is other package management tool developed by Red Hat and adopted by a number of other distributions like Fedora, Cent OS, SUSE/open SUSE, Oracle Linux, etc.
![[Pasted image 20250613211101.png]]

The higher-level package manager differs between distributions. RHFD historically use `dnf`(or old yum) while SUSE also use rpm but use the zypper interface.

The YAST in Open SUSE is similar to other graphical package manager. It is RPM based application. It is high level package manager. It is open SUSE's main tool for software and system configuration. It is like Ubuntu Software Software center or Pop Shop in PopOS. but YAST > PopOS as it has deeper system controls also, not just apps. 

![[Pasted image 20250613213028.png]]

## Common Application
1. Convert
2. Inkscape

## Command Line
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
## Finding Linux Documentation
![[Pasted image 20250619210931.png]]
Other documentation sources include Gentoo Handbook, Ubuntu Documentation or Fedora Documentation.
#### man
often-used source of Linux docs. They are present on all Linux distributions. It was introduced in early UNIX Versions. The name `man` just means `manual`.
###### manual chapters
#### GNU info
###### the GNU info system
###### using info from the command line
###### info Page Structure
###### the `--help` options or `-h` options
###### the help command
#### Other Documentation Sources
![[Pasted image 20250619221636.png]]

Most of the Linux desktop systems have a graphical help application. Usually have icon as question-mark or image of ship's life preserver. or type `gnome-help or yelp` or for KDE `khelpcenter`.
###### Package Documentation
It is also available as part of the package management system. Usually this docs is directly pulled from upstream source code. Such information is place under the `/usr/share/doc`. 
## Processes
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