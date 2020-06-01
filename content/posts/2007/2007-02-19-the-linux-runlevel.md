---
date: 2007-02-19
title: "The Linux Runlevel"
---







categories:
- RHCE


I'm pretty sure they don't provide the root password at the RHCE exam and ask you to deal with it :)

**Runlevels :**
`    *  0 - Halt the system
    * 1 - Single-user mode
    * 2 - Multi-user mode (without NFS)
    * 3 - Multi-user mode
    * 4 - Unused
    * 5 - Multi-user mode, graphical login
    * 6 - Reboot the system`



Switch runlevel while the system is running (example : switch to runlevel 5) :
`telinit 5`

**Boot in a different runlevel (say 5 again) :**

LILO : 
boot using "linux 5"

Grub :
Edit the kernel line by pressing "e" and add "5" to the end of that line. Press enter then "b" to boot with the new parameter.

**Changing the default runlevel :**
Edit /etc/inittab and edit this line, replace 3 by desired runlevel
`id:3:initdefault:`

**Resetting the root password : **
- Boot in runlevel 1
- passwd root

You're done.
