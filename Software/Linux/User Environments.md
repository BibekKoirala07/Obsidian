## Accounts, User and Groups
###### Identifying the Current User
As you know, Linux is a multi-user operating system, meaning more than one user can log on at the same time.
- To identify the current user, type **whoami**.
- To list the currently logged-on users, type **who**.
Giving **who** the **-a** option will give more detailed information.
###### User Start up files
When you open a terminal in Linux, your shell (usually **bash**) automatically runs a set of **startup files** to configure your environment.
These files set up how your shell behaves and what your environment looks like. They come from two main locations:
- **Global startup files** (in `/etc/`):  
    These apply to **all users** on the system.
- **User-specific startup files** (in your **home directory** like `~/.bashrc`, `~/.profile`, etc.):  
    These **customize or override** the global settings for just your account.

What can startup files do ? 
Startup files are powerful. They can:
- Customize your **shell prompt**
- Define **aliases** and command-line **shortcuts**
- Set your **default text editor**
- Configure your **`$PATH`** (so the shell knows where to find programs)
- Run any commands you want to happen automatically each time you open a shell
###### Order of the Startup Files
When you log in to a Linux system, your shell checks and runs several **startup files** to set up your environment. The order and behavior of these files can be confusing, so here’s a simple breakdown.

 **1. During Login (Login Shell)**
When you log in **via terminal (TTY), SSH**, or any **login shell**, the shell first evaluates:
- `/etc/profile` — applies system-wide settings for all users.

Then, it looks for the following **user-specific files**, in this exact order:
1. `~/.bash_profile`
2. `~/.bash_login`
3. `~/.profile`
    
The **first file it finds** is executed, and the rest are **ignored**.

> **Example**: If `~/.bash_profile` exists, `~/.bash_login` and `~/.profile` won’t be read.

🔹 Different Linux distributions may favor different files, and not all of them will be present by default.

2. When Opening a Terminal (Non-login Shell)
Whenever you **open a new terminal window** (like in a desktop environment) or **spawn a new shell**, it is usually **not a login shell**.
In this case, only:
- `~/.bashrc`
is executed.

That’s why most user-level configurations like **aliases, prompt tweaks, PATH changes**, etc., go into `.bashrc`.

3. Linking `.bashrc` with Login Shells
Since `.bashrc` is **not run by default** in a login shell, many users or distributions include the following line inside `.bash_profile` (or the equivalent):
```
if [ -f ~/.bashrc ]; then    . ~/.bashrc

fi

```

This ensures your `.bashrc` settings are always loaded, even during login.

![[Pasted image 20250622113729.png]]
###### Creating Aliases
It just means creating custom commands or modify existing ones to save time and keystrokes
```bash
	alias ll=`ls-la`
```
This creates a shortcut: typing `ll` will now run `ls -la`.
###### Basics of Users and Groups
Users
Each user on a Linux system is assigned:
- A **username** (e.g., `john`)
- A **user ID (UID)** — a unique number that identifies the user internally
    - Regular users typically have UIDs **1000 and above**    
    - System users (like services) have lower UIDs    

User info is stored in the `/etc/passwd` file.

example
```bash
	john:x:1002:1002:John Garfield:/home/john:/bin/bash
```
Here’s what each part means:
- `john`: username
- `x`: password (stored separately in `/etc/shadow`)
- `1002`: UID
- `1002`: GID (group ID, explained below)
- `/home/john`: home directory
- `/bin/bash`: login shell

Groups
A **group** is a collection of users who share the same access permissions for files and resources.
- Each user has a **primary group** (usually the same name and ID as the user).
- Users can also belong to **additional (secondary) groups**.

Group info is stored in `/etc/group`.

example
```bash
	john:x:1002:
```
This means:
- Group name: `john`
- GID: `1002`
- No additional members (besides the primary user)
###### Adding and Removing Users and Groups
You can add and remove users so nothing that thing. 
###### The root account
The root account is very powerful and has full access to the system. Other operating systems often call this the administrator account; in Linux, it is often called the superuser account. You must be extremely cautious before granting full root access to a user; it is rarely, if ever, justified. External attacks often consist of tricks used to elevate to the root account.

However, you can use **sudo** to assign more limited privileges to user accounts:
- Only on a temporary basis
- Only for a specific subset of commands.
###### su and sudo
When assigning elevated privileges, you can use the command **su** (**s**witch or **s**ubstitute **u**ser) to launch a new shell running as another user (you must type the password of the user you are becoming). Most often, this other user is root, and the new shell allows the use of elevated privileges until it is exited. It is almost always a bad (dangerous for both security and stability) practice to use **su** to become root. Resulting errors can include deletion of vital files from the system and security breaches.

Granting privileges using **sudo** is less dangerous and is preferred. By default, **sudo** must be enabled on a per-user basis. However, some distributions (such as Ubuntu) enable it by default for at least one main user, or give this as an installation option.
###### Elevating to root account
To temporarily become the superuser for a series of commands, you can type **su** and then be prompted for the root password.

To execute just one command with root privilege type **sudo <command>**. When the command is complete, you will return to being a normal unprivileged user.

**sudo** configuration files are stored in the **/etc/sudoers** file and in the **/etc/sudoers.d/** directory. By default, the **sudoers.d** directory is empty.

## Environment Variables
Environment variables are **key-value pairs** that define the **working environment** for the shell and other programs.
They store information like:
- The default shell
- Your home directory
- The path to executable files
- Language and locale settings
- Custom app configurations
An environment variable is just a **string**—a name and a value—that holds useful system information.
example
```bash
	HOME=/home/john
	EDITOR=nano				PATH=/usr/local/bin:/usr/bin:/bin
```
Where Do Environment Variables Come From ?
They can be:
- **Set by the system** (e.g., `PATH`, `HOME`, `SHELL`)
- **Set by users** in files like:
    - `~/.bashrc`
    - `~/.profile`
    - `~/.bash_profile`  
- Or **temporarily** set in the terminal:
```bash
	export MY_VAR="hello"
```

###### the path and shell variable
###### the ps1 variable and command line prompt
The PS lets you customize how command line prompt looks. 
`PS1` defines **what you see when your shell is waiting for input**.  
By customizing it, you can display useful information like your username, current directory, hostname, date, or even emojis.
You can use special placeholders to build your own prompt.
## Recalling Previous Commands
just type `history`.`
![[Pasted image 20250622123356.png]]
![[Pasted image 20250622123419.png]]

## File Permissions
###### File Ownership
In Linux and other UNIX-based operating systems, every file is associated with a user who is the owner. Every file is also associated with a group (a subset of all users) which has an interest in the file and certain rights, or permissions: read, write, and execute.

The following utility programs involve user and group ownership and permission setting:
![[Pasted image 20250622123532.png]]

###### File Permission Modes and `chmod` in Linux
In Linux, **every file and directory** has three types of permissions:

| Permission  | Symbol | What it allows                    |
| ----------- | ------ | --------------------------------- |
| **Read**    | `r`    | View file contents/list directory |
| **Write**   | `w`    | Modify file or directory          |
| **Execute** | `x`    | Run file or enter directory       |
|             |        |                                   |
|             |        |                                   |
These permissions are applied to **three categories of users**:

|Category|Symbol|Description|
|---|---|---|
|User (owner)|`u`|The user who owns the file|
|Group|`g`|Users who are in the same group|
|Others|`o`|Everyone else|