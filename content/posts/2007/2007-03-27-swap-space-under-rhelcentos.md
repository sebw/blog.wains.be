---
date: 2007-03-27
title: "SWAP space under RHEL/CentOS"
---







categories:
- RHCE


**Adding a 250 MB swap file to the system**

**Create an empty 250M file :**
`dd if=/dev/zero of=/swapfile bs=1024 count=256000`

**Create the swap on the newly created file :**
`mkswap /swapfile`

**Enable the new swap file :**
`swapon /swapfile`

**Edit fstab and add :**
`/swapfile swap swap defaults 0 0`

**Verify if the new swap space is enabled : **
`cat /proc/swaps
free -m`

A good idea is to have your swap partition as an LVM volume.. So you can always resize it if your needs are growing.
