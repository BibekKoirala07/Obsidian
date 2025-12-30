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


