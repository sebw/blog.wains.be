---
date: 2016-03-10
title: "Parallels- copy-paste not working from Mac to Fedora VM"
---

This one is weird but worked for me.

You should set SELinux as permissive, reboot, and set it back to enforcing

Edit `/etc/selinux/config` and change to

	SELINUX=permissive
	
Reboot

Set backup to enforcing

	SELINUX=enforcing
	
Reboot again.

Now copy-paste works for me. Let me know if it works for you :-)
