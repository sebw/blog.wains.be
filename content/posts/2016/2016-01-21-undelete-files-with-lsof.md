---
date: 2016-01-21
title: "Undelete files with lsof"
---

If you delete a file by mistake, there are several ways to recover it.

I'm going to explain how to recover a file that has been deleted but still "active" on the system because locked by a running process.

First, let's create a file maintained by a lock.

	$ watch ps aux > log.txt

Interrupt the process by hitting Ctrl+Z

ps reveals the "watch" command is still being executed but in T state (T = traced or suspended process).

	$ ps aux | grep watch
	sw       19290  0.0  0.0  17660  3620 pts/14   T    11:09   0:00 watch ps aux

The process ID (PID) is 19290.

	$ ls -l /proc/19290/fd
	total 0
	lrwx------ 1 sw sw 64 Jan 21 11:10 0 -> /dev/pts/14
	l-wx------ 1 sw sw 64 Jan 21 11:10 1 -> /home/sw/log.txt
	lrwx------ 1 sw sw 64 Jan 21 11:09 2 -> /dev/pts/14

Let's delete log.txt now

	$ rm log.txt

We can now see log.txt has been deleted, but the lock is still active.

	$ ls log.txt
	ls: cannot access log.txt: No such file or directory
	
	$ lsof -n | grep 19290
	[...]
	watch     19290               sw    1w      REG 8,6      7804     27151 /home/sw/log.txt (deleted)
	[...]

The interesting part here is "1w". It means it's the file descriptor number 1. The file can then be retrieved like this:

	$ cp /proc/19290/fd/1 /home/sw/recover.txt

You can return the process in the foreground using "fg" and kill it. 


