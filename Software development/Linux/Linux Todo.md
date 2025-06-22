## ✅ PHASE 1: Essential Commands & Navigation (Week 1–2)

🔹 Learn these basics until they become muscle memory:

|Topic|Command(s)|
|---|---|
|Current directory|`pwd`|
|List files|`ls -la`, `ls -lh`|
|Change directory|`cd`|
|Create file/folder|`touch`, `mkdir -p`|
|Copy/move/delete|`cp`, `mv`, `rm -rf`|
|View file contents|`cat`, `less`, `head`, `tail -n 20`|
|Search inside files|`grep "text" file`, `grep -r "text" ./folder`|
|Find files|`find . -name "*.js"`|
|Wildcards|`*.js`, `?.txt`|
|Permissions|`chmod +x`, `chown`, `ls -l`|
|File compression|`tar -czvf`, `unzip`, `gzip`, `xz`|
|Output redirect|`>`, `>>`, `|
|Disk usage|`df -h`, `du -sh .`|

🔁 Practice:

- Create folder trees and files
    
- Move/delete/search via terminal only
    
- Navigate with only keyboard (no GUI)
    

---

## ✅ PHASE 2: Package Management, Users, and SSH (Week 3–4)

🔹 Learn how to manage Linux system basics:

|Topic|Command(s)|
|---|---|
|Package management (Ubuntu)|`apt update`, `apt install`, `apt remove`|
|System info|`uname -a`, `uptime`, `free -h`, `top`, `htop`|
|Create user|`adduser username`|
|Switch user|`su username`, `sudo -i`|
|Grant sudo access|`usermod -aG sudo username`|
|SSH|`ssh user@host`, `scp file user@host:~/`|
|Generate SSH key|`ssh-keygen -t ed25519`|
|Start service|`systemctl start nginx`|
|Enable on boot|`systemctl enable nginx`|

🔁 Practice:

- SSH into a remote Ubuntu VPS (use any free-tier server like Oracle Cloud or a local VM)
    
- Create and manage users
    
- Install software (e.g., `htop`, `curl`, `git`, `nginx`)
    

---

## ✅ PHASE 3: Process, Services & Logs (Week 5–6)

🔹 Learn how servers actually run:

|Topic|Command(s)|
|---|---|
|Process list|`ps aux`, `top`, `htop`|
|Kill process|`kill -9 PID`, `pkill node`|
|Background jobs|`&`, `jobs`, `fg`, `bg`, `nohup`|
|Services|`systemctl status`, `restart`, `stop`|
|Logs|`journalctl -u nginx`, `tail -f /var/log/syslog`|
|Uptime|`uptime`, `who`, `w`|

🔁 Practice:

- Run a background Node.js app with `nohup` or `pm2`
    
- Restart and log systemd services
    
- View live logs with `tail -f`
    

---

## ✅ PHASE 4: Networking, Firewall, and Cron Jobs (Week 7–8)

🔹 Get comfortable with server-side automation and security:

|Topic|Command(s)|
|---|---|
|Network info|`ip a`, `hostname`, `ping`, `traceroute`|
|Ports|`netstat -tulpn`, `ss -tulwn`|
|Firewall|`ufw status`, `ufw allow 22`, `ufw enable`|
|Cron|`crontab -e`, `crontab -l`|
|Schedule task|`* * * * * /path/to/script.sh`|

🔁 Practice:

- Open/close ports with `ufw`
    
- Set a cron job that echoes something into a file every minute
    
- Check which services are listening to which ports
    

---

## ✅ PHASE 5: Bash Scripting & Customization (Week 9–10)

🔹 Automate your routine using Bash:

|Topic|Example|
|---|---|
|Variables|`NAME="world"`|
|Conditionals|`if [ $a -eq 1 ]; then ... fi`|
|Loops|`for i in {1..5}; do echo $i; done`|
|Functions|`my_func() { echo Hello; }`|
|Arguments|`$0`, `$1`, `$#`, `$@`|
|Aliases|`alias gs='git status'` (in `.bashrc`)|

🔁 Practice:

- Automate log backups
    
- Create `.sh` scripts for frequent setups
    
- Use `alias`, `export`, and functions inside `.bashrc` or `.zshrc`
    

---

## ✅ PHASE 6: Real-World Server Practice (Week 11–12)

🔹 Combine everything in real use:

|Task|Tools|
|---|---|
|Set up a Node.js/NestJS/Java app on VPS|SSH, `scp`, `nginx`, `systemctl`, `pm2`|
|Secure it|`ufw`, `fail2ban` (optional), SSH keys|
|Automate deployment|Bash + Git hooks or cron|
|Create a backup script|`tar`, `scp`, cron|

---

## 📦 Bonus Tools (Install & Explore)

- `tmux` – terminal multiplexer (advanced multitasking)
    
- `jq` – parse and format JSON
    
- `nmap` – check open ports
    
- `nc` – networking tool
    
- `rsync` – fast, safe file syncing
    
- `dig`, `nslookup` – DNS utilities
    

---

## ✅ Resources (All Text-Based, No Video)

- [explainshell.com](https://explainshell.com) — see what each command/flag does
    
- `man` pages: `man chmod`, `man grep` — built-in manual
    
- `tldr` pages: short, practical command examples (`apt install tldr`)
    
- [LinuxCommand.org](http://linuxcommand.org/)
    

---

Would you like me to build a **weekly structured plan with practice goals** for this 3-month roadmap?

4o


---

## 🧠 GOAL:

> Master Linux from the **command line** with **real-world server skills**, so you're job-ready for backend, DevOps, or AI work.

---

# ✅ 3-MONTH LINUX MASTERY PLAN

---

### 🗓️ WEEK 1–2: _Core Commands & Navigation_

**📚 Topics to Learn**

- Terminal navigation
    
- File & directory management
    
- Permissions
    
- Viewing/searching files
    
- Redirection & pipes
    

**🎯 Commands to Master**

bash

CopyEdit

`pwd, cd, ls -la, mkdir, touch, cp, mv, rm -rf cat, less, head, tail, grep, find chmod, chown, du -sh, df -h |, >, >>, tee man, history, clear`

**🛠️ Practice Tasks**

- Create folder: `projects/notes/` → move, copy, delete files
    
- Search for `.js` files with `find`
    
- Make a script that finds files >100MB and logs them
    

---

### 🗓️ WEEK 3–4: _Package Management, Users, SSH_

**📚 Topics to Learn**

- Installing software
    
- Users & permissions
    
- sudo access
    
- SSH & key-based login
    

**🎯 Commands to Master**

bash

CopyEdit

`apt update, apt install, apt remove adduser, usermod, passwd, su, sudo -i ssh, scp, ssh-keygen, whoami systemctl start/enable/status id, groups, useradd, userdel, groupadd`

**🛠️ Practice Tasks**

- Install `htop`, `curl`, `git`, `nginx`
    
- Create a new user with sudo rights
    
- SSH into a VPS (try free-tier Oracle, GCP, or a local VM)
    
- Generate SSH key and add it to `~/.ssh/authorized_keys`
    

---

### 🗓️ WEEK 5–6: _Processes, Services & Logs_

**📚 Topics to Learn**

- Running processes
    
- System services (systemd)
    
- Logs (syslog, journalctl)
    
- Background processes
    

**🎯 Commands to Master**

bash

CopyEdit

`ps aux, top, htop kill -9, pkill, jobs, fg, bg, nohup systemctl status/start/stop/restart journalctl -xe, tail -f /var/log/syslog`

**🛠️ Practice Tasks**

- Start `nginx`, check logs
    
- Kill a process (e.g. Node.js app), restart it
    
- Use `nohup` or `pm2` to run a script in background
    
- Log server uptime daily to a file
    

---

### 🗓️ WEEK 7–8: _Networking, Firewall, Cron Jobs_

**📚 Topics to Learn**

- IP/hostname, ports
    
- UFW firewall
    
- Crontab and automation
    

**🎯 Commands to Master**

bash

CopyEdit

`ip a, hostname, ping, traceroute netstat -tulpn, ss -tulwn ufw status, ufw allow/deny, ufw enable crontab -e, crontab -l`

**🛠️ Practice Tasks**

- Block and allow ports using `ufw`
    
- Schedule a cron job that backs up a folder every day
    
- Monitor which services are listening to ports (netstat)
    
- Ping/test connectivity to another host
    

---

### 🗓️ WEEK 9–10: _Bash Scripting & Customization_

**📚 Topics to Learn**

- Variables, loops, conditionals
    
- Functions and arguments
    
- Aliases, exports, `.bashrc`
    

**🎯 Commands to Master**

bash

CopyEdit

`echo, read, for, if, while $1, $#, $0, $@ alias, export`

**🛠️ Practice Tasks**

- Create a bash script to:
    
    - Clean temp files
        
    - Backup logs
        
    - Watch a process
        
- Add useful aliases in `.bashrc`
    
- Create a colored prompt
    

---

### 🗓️ WEEK 11–12: _Real-World Server Setup & Automation_

**📚 Topics to Learn**

- Deploying Node/Nest/Java app on VPS
    
- Automating with Bash + cron
    
- Server maintenance
    

**🎯 Skills to Apply**

bash

CopyEdit

`scp, pm2, systemctl, tar, crontab`

**🛠️ Practice Project**

- Deploy an API or app (NestJS, Node, or Java Spring)
    
- Run it with `pm2` or `systemctl`
    
- Set up a script to:
    
    - Backup the code + logs to another folder
        
    - Compress and rotate them
        
    - Send to remote server with `scp`
        

---

## 🧰 Final Bonus Tools to Try (Optional)

|Tool|Purpose|
|---|---|
|`tmux`|Split terminal, keep sessions|
|`jq`|Parse JSON from command line|
|`nmap`, `nc`|Network debugging|
|`dig`, `nslookup`|DNS checks|
|`rsync`|Fast remote sync & backup|

---

## 📌 Tip:

Keep a `linux-cheatsheet.md` file where you:

- Write the commands you learn
    
- Save reusable scripts
    
- Log what you do on servers
    
