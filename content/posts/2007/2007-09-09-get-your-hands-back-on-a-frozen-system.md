---
date: 2007-09-09
title: "Get your hands back on a frozen system"
---







categories:
- Linux


FOSSwire has an interesting article explaining how to get your hands back on your linux box when it appears to be badly frozen (yes it tends to happen on alpha releases of Ubuntu).

1. Hold down the Alt and SysRq (Print Screen) keys.
2. While holding those down, type the following in order. Nothing will appear to happen until the last letter is pressed: REISUB
3. Watch your computer reboot magically.


The key combination consisting of Alt, SysRq and another key, controls the command issued:

    * 0 through 9 sets the console log level, which controls the types of kernel messages that are output to the console.
    * b immediately reboots the system, without unmounting partitions or syncing.
    * c reboots kexec and outputs a crashdump.
    * e sends the SIGTERM signal to all processes except init (PID 1).
    * f calls oom_kill, which will kill a process that is consuming all available memory.
    * h, or any key which is not bound to a command, outputs a terse help document to the console.
    * i sends the SIGKILL signal to all processes except init.
    * k kills all processes on the current virtual console (and thus can be used to kill X and svgalib programs, see below). This was originally designed to imitate a Secure Access Key.
    * l sends the SIGKILL signal to all processes, including init.
    * m outputs current memory information to the console.
    * o shuts off the system.
    * p outputs the current registers and flags to the console.
    * r switches the keyboard from raw mode, the mode used by programs such as X11 and svgalib, to XLATE mode.
    * s attempts to sync all mounted filesystems.
    * t outputs a list of current tasks and their information to the console.
    * u attempts to remount all mounted filesystems in read-only mode.
    * v outputs Voyager SMP processor information.


**Links :**

[http://fosswire.com/post/fix-a-frozen-system-with-the-magic-sysrq-keys/](http://fosswire.com/post/fix-a-frozen-system-with-the-magic-sysrq-keys/)

[http://en.wikipedia.org/wiki/Magic_SysRq_key](http://en.wikipedia.org/wiki/Magic_SysRq_key)
