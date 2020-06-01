---
date: 2007-03-12
title: "CentOS + RAID with mdadm"
---







categories:
- RHCE


RHCE exam requires you to be able to create a RAID array on a running system, as well as with the installer.

I won't explain here how to deal with the installer as it's pretty easy.



**1. create the partitions**

Using fdisk or else
Set the ID type to "fd" (Linux RAID autodetect)

Say we got here /dev/hda2 and /dev/hdb2

**2. create the RAID array with the first drive only**

mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/hda2 missing

That's my way of dealing with the creation of an array. You can obviously create the array with the 2 drives now.

**3. Formatting the new array in ext3**

mke2fs -j -c /dev/md0

-j for ext3
-c for check

**4. Adding the other drive to the array**

mdadm --add /dev/md0 /dev/hdb2

You should hear the drives working. They are actually synching
Type : cat /proc/mdstat to see what is actually happening.

**5. Edit fstab**

Edit /etc/fstab

Add a line to get the array mounting when the system boots 

`/dev/md0 /raid ext3 defaults 0 2`
