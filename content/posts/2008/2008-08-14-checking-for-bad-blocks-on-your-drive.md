---
date: 2008-08-14
title: "Checking for bad blocks on your drive"
---







categories:
- Hardware
- Linux


If your partition is /dev/sda1

`e2fsck -cc /dev/sda1`

From the man pages :


    
    <code>       -c     This  option  causes  e2fsck to use badblocks(8) program to do a
                  read-only scan of the device in order to find  any  bad  blocks.
                  If  any  bad  blocks  are found, they are added to the bad block
                  inode to prevent them from being allocated to a file  or  direc‐
                  tory.   If  this  option  is specified twice, then the bad block
                  scan will be done using a non-destructive read-write test.</code>
