unix is brain of each devices

![[Pasted image 20261107212542.png]]

We can use `man syscalls` and can see all the system calls that are available. We get all system call. but what we type while doing command like `mkdir ..` isn't system call. that mkdir command is just C program which calls the mkdir of `syscalls`. You can see `man` command. make looks same but still. 

DPDK(take OS out of the equation for communicating with network card, storage, ram)

![[Pasted image 20261112163013.png]]
bashrc

![[Pasted image 20261112163524.png]]


.s stands for assembly. 

![[Pasted image 20261112181301.png]]


## Networking
each network card comes with its own mac address

![[Pasted image 20261112184949.png]]

ARP request.

so two people can have same public ip.

network portion and host portion
## 1. What is Unix?

Unix is an operating system originally developed at AT&T Bell Labs in the 1970s.

It introduced many concepts that are still fundamental to modern operating systems:

- Multiuser environment
    
- Multitasking
    
- File permissions
    
- Process management
    
- Shell and command-line interfaces
    
- Pipes and command composition
    
- Hierarchical filesystem


## 5. Something important for your backend career

I would recommend understanding these topics in Linux:

1. Processes and threads
    
2. File systems and permissions
    
3. Users and groups
    
4. Shell scripting
    
5. Networking (TCP, UDP, ports, sockets)
    
6. Process signals
    
7. Systemd and service management
    
8. SSH
    
9. Environment variables
    
10. Memory and CPU management