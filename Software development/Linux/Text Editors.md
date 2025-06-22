## nano and gedit
#### Overview of Text Editors in Linux
There are spectrum of choices.
As with most things in Linux, you're spoiled for choice — from minimalist editors to highly customizable powerhouses. Some of the most commonly used include:
- **`nano`**: A beginner-friendly, command-line editor. Very simple and intuitive for quick edits, especially for those new to the terminal.
- **`gedit`**: A graphical editor for GNOME-based environments. Easy to use, clean interface, and good for editing simple scripts or notes.
- **`vi`/`vim`**: A modal, terminal-based editor. Steep learning curve but extremely powerful once mastered. Commonly installed on nearly all Unix-like systems by default.
- **`emacs`**: A highly extensible and programmable editor. More than just an editor — it can function as a file manager, terminal emulator, debugger, and more.
![[Pasted image 20250621222316.png]]
#### Creating Files Without Using an Editor
```bash
	$ echo line one > myfile
	$ echo line two >> myfile
	$ echo line three >> myfile
```
```bash
	cat << EOF > myfile
	> line one
	> line two 
	> line three
	>> EOF
	$ 
```
#### nano and gedit
gedit is part of the GNOME desktop system(kwrite is associated with KDE). The gedit and kwrite editors are easy to use and are extremely capable. They are also very configurable. 
## More advanced Editors: vi and emacs
#### vi and emacs
Developers and administrators experienced in working on UNIX-like systems almost always use one of the two venerable editing options: **vi** and **emacs**. Both are present or easily available on all distributions and are completely compatible with the versions available on other operating systems. There are many more users of vi than there are of emacs
#### Introduction to vi
Usually, the actual program installed on your system is vim, which stands for **V**i **IM**proved and is aliased to the name vi. The name is pronounced as “vee-eye”.

Even if you do not want to use **vi**, it is good to gain some familiarity with it: it is a standard tool installed on virtually all Linux distributions. Indeed, there may be times when there is no other editor available on the system.

GNOME extends vi with a very graphical interface known as gvim and KDE offers kvim. Either of these may be easier to use at first.

==important==
Typing **vimtutor** launches a short but very comprehensive tutorial for those who want to learn their first vi commands.

Modes in vi
vi provides three modes, as described in the table below. It is vital to not lose track of which mode you are in. Many keystrokes and commands behave quite differently in different modes.
There are three modes with different features. They are 
Command, Insert and Line. They have their own features. 
