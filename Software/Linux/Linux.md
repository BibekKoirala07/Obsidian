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
## Shell and Bash
## Networking
`ip`, `ping`, `ss`, `dig`, `curl`, `traceroute`, `tcpdump`
## Package Management
## Services
## Logs
## Disks and Memory Management
## Text Editors



