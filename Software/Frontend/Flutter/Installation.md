We need to put environment variables so that flutter is recognized everywhere.
When docker is used, it locks down hardware virtualization resources.
### Hyper-V
Hyper-V is hardware virtualization technology( it gives memory to that thing ) build into Windows. It helps to create virtual machines on computer. With Hyper-V, we can run multiple operating systems or virtual machines at same time.
It is often used for running windows container, virtual servers, ==Docker Containers== or other OS like system with virtual environments on Windows.
### Intel HAXM
**Intel HAXM** is a **hardware-assisted virtualization engine** specifically designed for Android emulation on ==Intel processors==. It means it accelerates the android emulators by using CPU's hardware virtualization features make emulator run much faster. With out it emulator falls backs on slower, software-based virtualization.
### What happens when they conflict
When both **Hyper-V** and **HAXM** are trying to control your computer's hardware virtualization capabilities, but **they can’t be used at the same time**.
     **Hyper-V** locks down the hardware virtualization resources. Windows virtualization for virtual machines.
     **HAXM** needs to directly access the same resources to accelerate the Android emulator.
This is why when **Hyper-V** is enabled, **HAXM** can't function, causing the emulator to either fail to start or run much slower. 
### Why Hyper-V can't be disabled when using Docker ? 
Because it uses Hyper-V, To disable Hyper-V We need to follow series of long tasks. We need to setup to go to **HAXM** which is long and tedious process so it isn't possible. Instead we need to live with slow emulators. 

==There is a options in Android Studio to set up whether we want to use hardware or software while setting emulator. Do scroll lil bit to see there==.



