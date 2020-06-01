---
date: 2006-12-18
title: "Bash shortcuts"
---







categories:
- Bash
- Linux


Great memo !
[Original link](http://www.howtogeek.com/howto/ubuntu/keyboard-shortcuts-for-bash-command-shell-for-ubuntu-debian-suse-redhat-linux-etc/)

25 March 2007 : New memo from [http://linuxhelp.blogspot.com/2005/08/bash-shell-shortcuts.html](http://linuxhelp.blogspot.com/2005/08/bash-shell-shortcuts.html)

**CTRL Key Bound**

Ctrl + a - Jump to the start of the line
Ctrl + b - Move back a char
Ctrl + c - Terminate the command
Ctrl + d - Exit the current shell
Ctrl + e - Jump to the end of the line
Ctrl + f - Move forward a char
Ctrl + h - Same as backspace
Ctrl + k - Delete to EOL
Ctrl + l - Clear the screen
Ctrl + r - Search the history backwards
Ctrl + R - Search the history backwards with multi occurrence
Ctrl + t : Swap the last two characters before the cursor
Ctrl + u - Delete backward from cursor
Ctrl + w : Delete the word before the cursor
Ctrl + xx - Move between EOL and current cursor position
Ctrl + x @ - Show possible hostname completions
Ctrl + z - Suspend/ Stop the command. "fg" restores the suspended command.

**ALT Key Bound**

Alt +  - Move to the last line in the history
Alt + ? - Show current completion list
Alt + * - Insert all possible completions
Alt + / - Attempt to complete filename
Alt + . - Yank last argument to previous command
Alt + b - Move backward
Alt + c - Capitalize the word
Alt + d - Delete word
Alt + f - Move forward
Alt + l - Make word lowercase
Alt + n - Search the history forwards non-incremental
Alt + p - Search the history backwards non-incremental
Alt + r - Recall command
Alt + t - Move words around
Alt + u - Make word uppercase
Alt + back-space - Delete backward from cursor

**More Special Keybindings**

Here "2T" means Press TAB twice

$ 2T - All available commands(common)
$ (string)2T - All available commands starting with (string)
$ /2T - Entire directory structure including Hidden one
$ 2T - Only Sub Dirs inside including Hidden one
$ *2T - Only Sub Dirs inside without Hidden one
$ ~2T - All Present Users on system from "/etc/passwd"
$ $2T - All Sys variables
$ @2T - Entries from "/etc/hosts"
$ =2T - Output like ls or dir
