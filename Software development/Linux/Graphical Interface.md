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

