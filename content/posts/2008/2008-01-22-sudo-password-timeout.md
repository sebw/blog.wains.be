---
date: 2008-01-22
title: "sudo password timeout"
---







categories:
- Linux


By default, sudo keeps the password in "memory" for 5 minutes (at least under Ubuntu).

If you want to change that value

`$ sudo visudo`

Add the following line before the other "Defaults" line : 
`Defaults timestamp_timeout=2`

Or simply append "timestamp_timeout=2" to the existing Defaults line like :
`Defaults        !lecture,tty_tickets,!fqdn,timestamp_timeout=2`

This will set the timeout to 2 minutes...

Set to 0 to always require the password
Set to "-1".. you will only have to prove you know the password once.

**Options on the Defaults line are system-wide settings.**

If you want to apply settings to particular users add :
`Defaults:johndoe  timestamp_timeout=-1`

If you don't want to be prompted for the password at all, you must have a rule like
`johndoe ALL=(ALL) NOPASSWD: /bin/mount`

This would never require the password for the command "mount".

If you don't want to ever be asked for the pass when using sudo :

`johndoe ALL=(ALL) NOPASSWD: ALL`

