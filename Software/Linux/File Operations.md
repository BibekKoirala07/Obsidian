Linux treats everything as file. just like in UNIX-like operating systems. 
## File system
#### Introduction to File systems
In Linux and other UNIX-like systems, everything is treated like a file — from text documents to hardware devices like keyboards, printers, and even your system memory. This design makes things surprisingly simple: once you know how to read and write a file, you already know how to interact with many parts of the system!
For example:
- Want to check what's happening with your sound card? Read a "file" in `/dev`.
- Need to send data to a printer? Just write to a "file" that represents it.

Even directories are special kinds of files. This uniform approach to input/output (I/O) is a core reason Linux is so flexible and powerful.

The Linux File system is structured like an upside-down tree:
It starts at a single point called the root directory, written as just `/`.
From there, it branches into sub directories like `/home`, `/usr`, `/etc` and more.

==Important==
Don't confuse the root directory `/` with the root user. `/` is the top of the file structure. and `root` is the system administrator user(with full permissions).

The computer storage is usually divided into partitions. Each partition can hold its own file system. Linux doesn't assign drive letters like Windows (`C:`, `D:`). Instead, it mounts partitions to specific locations in the tree. Example. 
- Your main partition might be mounted to `/`
- A separate partition for home directories might be mounted to `/home`
- A USB drive might show up under `/media/username/`

This makes the system feel seamless—no matter which drive something lives on, it all appears as part of the same tree.

#### File system varieties
Linux is not tied to just one filesystem — instead, it supports **a wide range of filesystems**, each suited to different needs. Some were **designed for Linux**, while others come from **different operating systems**.

These are file systems created specifically for Linux:
- **ext3** – Older, journaling filesystem; reliable but mostly outdated.
- **ext4** – The default on most Linux distros today; fast, stable, and widely used.
- **btrfs** – Advanced features like snapshots and self-healing; good for large-scale systems.
- **squashfs** – Read-only and compressed; often used in live CDs and embedded systems.
Think of ext4 as your reliable all-purpose vehicle, while btrfs is like a futuristic self-driving car with diagnostics built in.

And File systems from other OSes
Linux plays well with others too - it supports many foreign file systems. so you share data across platforms. 
For Windows
- ntfs - Modern Windows Filesystems
- vfat/exfat - Used in USB drives, SD Cards
For MACOS
- hfs/hfs+ - Older Mac file systems
For Others
- xfs - From SGI, designed for scalability and performance
- jfs - IBM's journaling file system
These make it easy to plug in USB drives from other systems and access files seamlessly.

You might think one file system is enough—but in practice, multiple types are often used on the same machine. Why?

Based on:
- **File size patterns** (e.g., small vs. large files)
- **Modification frequency** (e.g., log files vs. static content)
- **Speed requirements**
- **Type of hardware** (e.g., SSD vs. HDD vs. USB)
For example, ext4 might be used on your root partition, while a USB might be formatted with vfat to ensure Windows compatibility.

Journaling File system: Safer and Smarter
Modern Linux systems use journaling file systems like
- ext4
- xfs
- btrfs
- jfs
What's journal ling ? 
It means changes are recorded in a "journal" before being applied. this protects data in case of power failure or crashed and prevent file system corruption. 

==Exciting things==
Network and Distributed File systems
Linux can also access files across networks, as if they're part of the local system. this is crucial for servers and cloud infra. 
- **NFS (Network File System)** – Widely used for sharing files in UNIX/Linux environments
- **Ceph** – Scalable and distributed; often used in data centers
- **Lustre** – High-performance, used in supercomputing
- **OpenAFS** – Cross-platform, used in some academic and enterprise networks.
This is how massive systems like Google or scientific clusters work—files live _everywhere_ but feel local.

#### Linux Partitions
In most Linux systems, **each filesystem lives inside its own partition**. A partition is simply a reserved section of your hard drive where Linux stores data in an organized way. Example
- `/` → Root partition: contains the core of the Linux system
- `/home` → User data partition: stores personal files and settings
- `/tmp` → Temporary files partition: used during software installs or system processes
This separation isn’t just for tidiness—**it’s smart system design**.

What is the need of Multiple Partitions ? 
Partitions help **organize** data and provide **isolation**. This has some practical advantages:

| Benefit           | Explanation                                                                                         |
| ----------------- | --------------------------------------------------------------------------------------------------- |
| Stability         | If one partition (like `/home`) runs out of space, the rest of the system (like `/`) keeps running. |
| Security          | If something corrupts `/tmp`, your user files in `/home` stay untouched                             |
| Backup & Recovery | You can format or restore one partition without touching the rest.                                  |
| Performance       | Fast-access areas (e.g., `/var` or `/tmp`) can be on faster disks                                   |
Real world use ? 
Let’s say you have a Linux laptop running both **RHEL 9** and **RHEL 8**. Using a tool like **GParted**, you’d see multiple partitions—some shared, some specific to each OS:
- RHEL 9’s `/` and `/home`
- RHEL 8’s `/` and `/home`
- A shared **swap** partition
- EFI or boot partitions for boot management

There’s also a more advanced trick called a **loopback file**, where a filesystem exists inside a file on another filesystem—but we’re skipping that for now. You’ll bump into it later when learning about things like containers or disk images.

TLDR, This improves stability, security, and system recovery
#### Mount Points
Before you can **use** a filesystem (whether it's on a hard disk, USB stick, or even a network share), you need to **mount** it. But what does that mean?

A **mount point** is simply a **directory** where a filesystem gets attached to the main filesystem tree.

Think of the Linux filesystem as a big tree starting at `/`. When you plug in another filesystem (say, from a USB), you're "grafting" a new branch onto that tree. The **spot where you attach it** is the mount point.

Example:
If you mount a USB drive at `/mnt/usb`, all the files from that USB will appear inside `/mnt/usb`.

==Warning==
If you mount a filesystem on a **non-empty directory**, the original contents of that directory are **temporarily hidden** (but not deleted).  They only become visible again after you unmount the filesystem.
That’s why mount points are usually **empty directories** like:
- `/mnt`
- `/media`
- `/run/media/username/USB_NAME`

Common Use Cases

| Action                      | What happens                                                               |
| --------------------------- | -------------------------------------------------------------------------- |
| Plug in a USB               | Linux auto-mounts it at a mount point (like `/media/...`)                  |
| Mount manually              | You can use `mount` command to attach a filesystem to a specific directory |
| Create your own mount point | Just use `mkdir /mnt/mydrive` (make sure it’s empty!)                      |
Mount points give Linux the flexibility to: 
- Seamlessly integrate **multiple filesystems** into a single tree
- Attach or detach drives **on-the-fly**
- Control where external or internal data is accessed

![[Pasted image 20250621121936.png]]
#### Mounting and Unmounting
Before you can access files on a drive or partition, Linux needs to **mount** it—attach it somewhere in the filesystem tree. And when you're done, you should **unmount** it to safely disconnect it.

To mount a file system, we can use the `mount` command. The basic format is 
```bash
	sudo mount [device] [mount_point]
	sudo mount /dev/sda5 /home
```
“Attach the filesystem on `/dev/sda5` and make it accessible through the `/home` directory.”

You can also mount by:
- **Disk label** (e.g., `LABEL=mydisk`)
- **UUID** (Universally Unique IDentifier)(more reliable) — useful because device names like `/dev/sda5` can change across boots

When we are doing using a filesystem (like a USB drive), you should unmount it to avoid data loss.
```
	sudo umount /home
```
- The command is `**unmount**`, not `unmount`!
- You must close any files or terminals using the mount point before unmounting.

Who can mount and unmount
By default, **only the root user** (or someone using `sudo`) can mount or unmount filesystems. However, systems can be configured (via `/etc/fstab` or `/etc/sudoers`) to allow specific users or groups to do this.

Auto Mount at Boot: /etc/fstab
If you want a partition to be mounted **automatically** every time the system boots, you need to add it to the **`/etc/fstab`** file. This file lists:
- What filesystems should be mounted
- Where they should be mounted
- With what options

View Mounted File systems
There are two ways
```bash
	mount
	df -TH // df = Disk free and -T = Show file system and h = Human-readable sizes(like GB, MB)
```
This command shows
- Mount points
- Filesystem types
- Disk space usage

What is `tmpfs` ?
In the output of `df -Th`, you might see entries of type **`tmpfs`**. 
These aren’t real disk filesystems—they're **temporary filesystems stored in RAM**.
 tmpfs is used for:
- Performance (RAM is faster than disk)
- Temporary data that doesn't need to persist after reboot
- System components like `/run`, `/tmp`, etc.
#### NFS and Network File systems
In the real world, data often needs to be **shared across different machines**—whether they’re in the same office or spread across the globe. That’s where **Network Filesystems** come in.

What is Network File system
A **Network Filesystem** lets computers access files **over a network** as if they were stored locally. Think of it as:
> “Your files live on another machine, but you can open and edit them as if they were on your own computer.”

Network filesystems can store data.
- Entirely on one **central server**
- Or **distributed** across multiple machines (nodes)

This flexibility is crucial for businesses, development teams, and anyone managing a cluster of machines.

Why use a network file system ? 
- **Consistency**: Users can access the **same files** from any machine they log in to.
- **Centralization**: All user data can be stored and backed up from **one place**.
- **Configuration Sharing**: Users’ config files (e.g., `.bashrc`, `.vimrc`) follow them across systems
- **Light Clients**: Thin client systems can run with minimal local storage.

![[Pasted image 20250621123345.png]]

Most Common Network Filesystems

| File Systems | Origin                      | Best for                       |
| ------------ | --------------------------- | ------------------------------ |
| NFS          | Sun Microsystem(UNIX/Linux) | Linux/UNIX environments        |
| CIFS/Samba   | Microsoft (Windows roots)   | Windows–Linux interoperability |
How NFS Works (in a Nutshell)
1. A server **exports** (shares) a directory via NFS.
2. A client system **mounts** that shared directory to a local mount point.
3. The client can now read/write files over the network just like a local drive.
example
```bash
	sudo mount server.example.com:/exports/home /home
```
Now the user’s `/home` directory is being served **over the network**!

Real-World Use Case:
System administrators often:
- Set up a central server with users’ home directories.
- Configure all lab or office machines to mount those directories via NFS.
- This way, users can log into **any machine** and see **their own files**—just like logging into a cloud account.
#### NFS on the server
lets set up the file sharing server
To share files over a network using **NFS (Network File System)**, you first need to set it up on the **server**—the machine that owns the files and will allow others to access them.

Step 1: Start the NFS Server
NFS runs using background services called **daemons**. You start them using `systemctl`:
```bash
	sudo systemtcl start nfs // in debian
	sudo systemtcl start nfs-server
```
This command starts the NFS server process, allowing it to begin sharing files.

Step 2: Define what to share - `/etc/exports`
The file **`/etc/exports`** is the configuration file where you specify:
- Which **directories** you want to share
- Who can access them
- What **permissions** they get
Example entry
```bash
	/projects *.example.com(rw)
```
This means:
- The `/projects` directory is being shared
- All systems in the `example.com` domain can access it
- They get read/write (rw) access

Step 3: Apply the changes
After editing `/etc/exports`, tell NFS to refresh its list of shared directories:
```bash
	sudo exports -av
```
This re-reads the config file and updates shared folders **without restarting** the NFS service.It’s lighter and faster than a full restart. However if needed, you can restart the NFS server
```
	sudo systemtcl restart nfs
```
This **temporarily stops NFS**, so avoid doing it while clients are connected—use `exportfs -av` for lighter changes.

Step 4: Auto-Start NFS on Boot
To make sure the NFS server **automatically starts** when the system reboots:
```
	sudo systemtcl enable nfs or nfs-server
```
In Linux, every user has their own personal space on the system - called their home directory. A **home directory** is where a user's personal files, settings, and data live. It’s where you land when you log in, and where most of your daily work happens. For most users, it's found under
```
	/home/username
```
You can store:
- Documents
- Downloads
- Config files (like `.bashrc`, `.vimrc`)
- Custom scripts
Each user has full control over their own home directory, but not over others’.

Special Case: `/root` Directory
The **`/root`** directory (not to be confused with the `/` root of the filesystem!) is the **home directory of the root user**—Linux's superuser or system administrator.
So:
- `/root` - Superuser's private home
- `/home/username` - Regular user's home

Multi-User Systems & Grouping
On multi-user systems (like schools, companies, or shared servers), you can organize users by department or role. Example layout:
```
	/home/faculty
	/home/staff
	/home/students
```
Then inside each group directory, users can be assigned sub folders
```bash
	/home/students/alice
	/home/students/bibek
```
This helps with organization, permissions, and administrative policies.
![[Pasted image 20250621131355.png]]

Home as a separate File systems
System administrators often:
- Mount `/home` on its own partition
- Or even share it across a network using NFS
Why ? 
- Easier to back up and manage
- Keeps system files (like `/`) safe if `/home` gets full
- Users can log in from any machine and still get their environment

#### The /bin and /sbin Directories
When you run commands like `ls`, `rm`, or `ip`, where do they actually live on your system?  
Answer: In special directories like **`/bin`** and **`/sbin`**—the home of **essential system commands**.

What's inside `/bin` ?
The **`/bin` (binaries)** directory contains **crucial user commands** needed for:
- Booting the system
- Operating in single-user mode
- Everyday use by all users
Command commands are `ls cp mv cat rm ps`. you can see them using `ls /bin`.

What inside `/sbin` ?
The `/sbin` (system binaries) directory is meant for system administration commands, mostly used by the root user. Command commands are `fsck ip reboot mount shutdown`. We can view them with `ls /sbin`.

What about `/use/bin` and `/usr/sbin` ?
Historically, these directories were meant for non-essential binaries:
- `/usr/bin` - Additional user programs
- `/usr/sbin` - Additional system admin tools
The idea was that `/usr` could be on a separate disk or network volume, mounted after the system booted.
==Important==
But on most Linux. It's all merged. on most Linux distributions, you'll find that
```
	/bin - is a symbolic link to /usr/bin
	/sbin - is a symbolic link to /usr/sbin
```
Why ? Because:
- The old separation isn’t tested anymore 
- Most systems can’t even boot properly without merging them
- It simplifies the system layout
You can check ? 
```bash
	ls -l /bin

	# You'll likey see somthing like:
	/bin -> usr/bin
```
#### The /proc File system
It is a window into the kernel.
The `/proc` directory is **not a normal filesystem**. It’s part of what’s called a **pseudo-filesystem**—meaning it **doesn’t store actual files on disk**, but instead **shows you live information** about your running system. 

Think of `/proc` like a **dashboard** built from live data inside the Linux **kernel's memory**. Every file or directory you see inside `/proc` represents **something happening right now** in your system—like CPU stats, memory usage, or info about running processes.
- It's virtual
- it updates live
- It uses zero disk space

Here are virtual files in `/proc`.
Here are some key entries and what they tell you.

| File               | What it shows                               |
| ------------------ | ------------------------------------------- |
| `/proc/cpuinfo`    | Info about your CPU (model, cores, speed)   |
| `/proc/meminfo`    | Memory usage (RAM total, used, free)        |
| `/proc/mounts`     | All currently mounted filesystems           |
| `/proc/partitions` | Info about available disk partitions        |
| `/proc/interrupts` | List of hardware interrupts used by devices |
| `/proc/version`    | Kernel version and Linux distribution info  |
|                    |                                             |
We can use it `cat /proc/cpuinfo`.

Special Directories in `/proc`.
###### `/proc/<PID>/`
For each running process, there's a folder named after its process ID(PID):
```
	/proc/1234/
```
Inside, you'll find real-time info that specific process
- What command started it
- It's memory usage
- Open files
- CPU time consumed
Example
```
	cate /proc/$$/cmdline
```
(shows the command that started your current shell).

###### `/proc/sys/`
This is where tunable system settings live—organized in directories.
You can:
- Read system parameters
- Even change some of them (with root access)
Try this 
```
	cate /proc/sys/kernel/hostname
```
or you can change it.

Why `/proc` Is So Powerful
- **No storage overhead** – Everything is generated **on demand** from memory
- **Always up-to-date** – Shows exactly what’s happening in the system _right now_
- **Readable like files** – You don’t need special tools to inspect your system
- **Used by tools** – System commands like `top`, `ps`, `free`, `uptime` use `/proc` under the hood
#### The /dev Directory
It is about connecting devices to files.
In Linux, **everything is treated like a file**—even your hardware devices. The `/dev` directory is where those **device files** live.

What's in `/dev` ?
The `/dev` directory holds **device nodes**, which are special files used to interact with **hardware** and **virtual devices** on your system.
> "Handles or gateways to physical or virtual devices"

They aren’t real files in the usual sense—they don’t store data. Instead, they represent devices and let you send data to or receive data from them.

| File        | What it responds                                            |
| ----------- | ----------------------------------------------------------- |
| /dev/sda1   | The **first partition** of the first hard drive             |
| /dev/lp1    | The **second printer** connected to the system              |
| /dev/random | A source of **random numbers**, often used for cryptography |
| /dev/null   | A "black hole" where output goes to be discarded            |
| /dev/tty    | Represents terminal devices (your screen or console)        |
==Important ==
It is managed by udev. The contents of `/dev` aren’t fixed or manually created. Instead, they are managed by a system called **`udev`** (short for _userspace device manager_), which:
- Detects hardware in real time (like USBs or disks)
- Creates the correct device nodes dynamically
- Removes them when the device is unplugged
That’s why `/dev` appears:
- **Empty** when unmounted
- **Full of device files** when the system is running
This dynamic behavior makes Linux **more flexible and portable** across hardware types.

#### The /var directory
The name `/var` stands for **"variable"**, and that tells you a lot:  
It holds files that **change over time** as your system runs—logs, caches, print jobs, updates, and more.
This directory is the **dynamic part of your filesystem**.

What lives in `/var` ? Here are the key sub directories and what they're used for:

| Directory  | What is contains                                                                                    |
| ---------- | --------------------------------------------------------------------------------------------------- |
| /var/log   | **System logs** – records of what the system and applications are doing (e.g. `syslog`, `auth.log`) |
| /var/lib   | State data – package manager files, system databases (e.g., `dpkg` info on Debian/Ubuntu)           |
| /var/spool | **Spools** – files waiting to be processed (print queues, mail queues)                              |
| /var/tmp   | Temporary files - longer lived temp data(persists after reboot, unlike `/tmp`)                      |
| /var/ftp   | Data served by an FTP server(if installed)                                                          |
| /var/www   | Web server files(for Apache, Nginx)                                                                 |
So, Why might `/var` be on its own file system ? 
Because files in `/var` can **grow unpredictably**—especially logs or cached updates—many system admins:
- Mount `/var` on a separate partition
- This prevents runaway growth from affecting critical system areas (like `/`)
Example: If a log file grows endlessly and fills up the disk, putting `/var` on its own partition ensures it **won’t crash the whole OS**.
![[Pasted image 20250621135211.png]]
How `/var` works in the background ? 
- Logs are written **constantly** (e.g., every time you boot, connect to Wi-Fi, run `sudo`)
- Package updates write to `/var/lib`
- Print jobs land in `/var/spool` before being sent to a printer
- Web servers serve files from `/var/www`
Yet as a user, you don’t interact with `/var` much — unless you're debugging, administering, or hosting services.
#### The /etc Directory
In Linux, the **`/etc`** directory is where all system-wide configuration files live.

What Is `/etc` Used For?
- It stores **settings and configuration files** for your system, services, and applications.
- It does **not** contain programs or binaries.
- Some entries may be **scripts**, but the main focus is **text-based config files**.
Examples: 
- Network settings
- User account information
- System service configurations
- Time zone, hostname, DNS, etc

###### Important Files in `/etc`

|File|Purpose|
|---|---|
|`/etc/resolv.conf`|Tells the system **which DNS servers** to use|
|`/etc/passwd`|Stores basic info about **user accounts**|
|`/etc/shadow`|Contains **encrypted user passwords** (protected)|
|`/etc/group`|Defines **user groups**|
|`/etc/fstab`|Filesystem mount info for boot|
|`/etc/hostname`|Defines your computer's **host name**|
|`/etc/os-release`|Info about your Linux **distribution**|

###### Sub directories to Know

|Directory|Purpose|
|---|---|
|`/etc/systemd/`|Configs for systemd services and units|
|`/etc/network/` or `/etc/netplan/`|Network interface config (varies by distro)|
|`/etc/ssh/`|Config files for SSH server/client|
|`/etc/sysconfig/`|Used on Red Hat/SUSE-based systems for extra config|
|`/etc/cron.*`|Cron job definitions (scheduled tasks)|
Permissions and Scope
- Only the **superuser (root)** can modify files inside `/etc`.
- These settings affect the **entire system**, not just one user.
- Regular users cannot change these—this prevents accidental system misconfigurations.

But what about user-specific Configs ? 
Those live in **dot files inside home directories**, e.g. :
- `~/.bashrc`
- `~/.gitconfig`
- `~/.vimrc`
So :
- `/etc` = **global (system-wide)** config
- `~/.config` or `~/.*rc` = **user-specific** config
#### the `/boot` directory
Starting point of the system. The **`/boot`** directory holds **everything needed to start (boot) your Linux system**—before the main operating system even begins to load.

Why `/boot` Is Sometimes on Its Own Partition ?
Because booting is such a **sensitive** process:
- `/boot` is sometimes kept on a **separate partition** to ensure it’s always available and not affected by encrypted or complex root filesystems.
#### The /lib and /lib64 Directories
The libraries that power the system
In Linux, many programs don’t include all the code they need inside themselves. Instead, they **reuse common code** stored in shared files called **libraries**. That’s exactly what the `/lib` and `/lib64` directories are for.

What library ? 
Think of libraries as **building blocks** or **toolboxes** that programs use. Instead of copying the same code into every program, the system stores it once in a **shared location**, and programs "borrow" from it at runtime. These shared files are known as:
- Shared Libraries
- Shared Objects
- Often have a `.so` extension (short for **Shared Object**)
Example
```bash
	/lib/librncurses.so.5.9
```
This is a shared library that helps programs handle text-based UIs in the terminal.

What’s in `/lib` ?
- Contains libraries needed by essential system programs
    - These are the programs found in `/bin` and `/sbin`
- Also contains important low-level system tools.
- You’ll typically see files starting with `lib` or `ld`
View them with: 
```bash
	ls /lib
```

What’s in `/lib64` ?
- Specifically holds 64-bit versions of libraries
- Used by systems running a 64-bit Linux kernel
- Meanwhile, `/lib` may contain 32-bit versions (on multi-arch systems)
So:
- On modern 64-bit Linux systems, both `/lib` and `/lib64` are often present.
- Programs choose the right one based on what architecture (32-bit or 64-bit) they were built for.

How Programs Use These Libraries
When a program runs, the system uses a **dynamic linker** (like `ld-linux.so`) to locate and load the necessary `.so` files from `/lib`, `/lib64`, and other library paths.
This is why these directories are essential — if these libraries are missing or corrupted, critical programs like `ls` or `cp` might not even run!
#### Removable media: the /media, /run and /mnt directories
When you plug in a **USB drive**, **CD**, or other **external device**, your Linux system doesn’t just magically “know” what to do with it. Instead, it **mounts** it—meaning it attaches the device to your **filesystem** so you can access it like any other folder.
Linux provides **special directories** for this task: `/media`, `/run`, and `/mnt`.


#### Additional Directories Under /:
There are some additional directories to be found under the root directory: ![[Pasted image 20250621203103.png]]

#### The /usr Directory Tree
The **/usr** directory tree contains theoretically non-essential programs and scripts (in the sense that they should not be needed to initially boot the system) and has at least the following sub-directories: ![[Pasted image 20250621203157.png]]
## Comparing Files and File Types
#### Comparing Files with diff
The **`diff`** command is a powerful tool used to:
- Compare two text files
- Show the **differences** between them
- Help track changes, debug problems, or review file edits
It's **text-focused**—not ideal for images or binary files.

simply 
```bash
	diff file1.txt file2.txt
```
There are options and you can search that in meantime. 
#### Using diff3 and patch
When multiple people are editing the same file—or you're trying to keep an old version in sync with a new one—you don’t want to manually go line-by-line to spot differences or copy-paste changes. That’s where **`diff3`** and **`patch`** come in. While `diff` compares **two files**, `diff3` compares **three**.

The `patch` command is used to **apply changes** to files, using a **patch file**.
A **patch file** is a text file that describes the **difference** between two versions of a file or folder. It’s created using `diff`.
#### Using the file utility
In Linux, file extensions don’t determine the file type like they do in Windows.
- Just because a file is called `notes.txt` doesn't _guarantee_ it’s a text file.
- Likewise, a file named `runme` might be an executable—even with no `.exe`!
That’s because **Linux doesn't rely on the file name**, but rather on **what's inside** the file.

What does the `file` command do ? 
The **`file`** utility helps you **figure out what kind of data** a file actually holds. It looks inside the file’s content, not just its name, and checks for:
- Human-readable text
- Executable code
- Image formats
- Archives like `.zip`, `.tar`, `.gzip`
- Scripts (e.g. Bash, Python)
- Libraries (`.so` files)
- Device nodes or special files
```
	file filename
```
## Backing Up and Compressing Data
#### Backing Up Data
Backing up data in Linux can be done using simple tools like `cp` and more advanced ones like `rsync`. The `cp` command is straightforward and useful for local copies, but it always copies all files regardless of whether they’ve changed, which can be inefficient for large directories. It's limited to local filesystems unless you're copying to a mounted remote location like NFS. On the other hand, `rsync` is much more efficient and flexible. It checks if a file has changed by comparing size and modification time and only transfers changed files, making it ideal for syncing large directory trees. It can also copy data over a network using SSH, with remote paths specified as `user@host:/path`. A common command looks like `rsync -av source/ dest/`, where `-a` preserves file permissions and timestamps, and `-v` shows progress. Adding `--delete` will ensure the destination matches the source by removing extra files. `rsync` is especially powerful for incremental backups and remote synchronization, making it the preferred tool for reliable, efficient backups in Linux.
#### Using rsync
`rsync` is a powerful and efficient tool used for syncing files and directories, both locally and across networks. A typical usage might look like `rsync -r project-X archive-machine:archives/project-X`, which recursively copies a project folder to a remote archive system. However, `rsync` can also be destructive if used incorrectly—wrong paths or misunderstood options can result in data loss or overwriting important files. It’s always a good idea to first test any `rsync` command with the `--dry-run` option, which simulates the operation without making actual changes. The general syntax is `rsync source destination`, where either can be local or remote. A commonly recommended option set for backups is: `rsync --progress -avrxH --delete sourcedir destdir`. This combination enables archive mode (`-a`), verbose output (`-v`), recursive copy (`-r`), itemized changes (`-x`), preserves hard links (`-H`), and deletes extraneous files in the destination.
#### Compressing Data
#### Compressing Data using gzip
#### Compressing Data using bzip2
#### Compressing Data using xz
#### Handling Files using zip
#### Archiving and Compressing Data using tar
#### Relative Compression Times and Sizes
#### Disk-to-Disk Copying (dd)