---
date: 2005-10-27
title: "Moving a folder to a new partition"
---

Let's say you have enough remaining free diskspace on your harddrive, you may want to move directories like /home or /tmp to a new partition...

**YOU MUST BE ROOT UNDER TTY AND ANY OTHER USER MUST BE LOGGED OFF**

`- fdisk -l /dev/hda --> you'll see the list of your current partition setup
- fdisk /dev/hda --> the drive you want to allocate the new partition
- press m to display the menu --> it will show some error messages, if you have a recent computer, you can avoid the messages and press m
- press n to create a new partition
- if you already have 3 partitions (let's say /boot, / and swap), you will need to create an extended partition
- it will give you the opportunity to choose from which cylinder the partition should begin, fdisk will guess the default value, just - press enter
- last cylinder, same as above, press enter --> the extended partition has been created : /dev/hda4
- back to the menu, press c to create a new partition
- select "logical partition"
- enter the size of the partition (+5000M for 5 Gb) --> the logical partition has been created : /dev/hda5
- back to the menu, press w to save the changes`

Reboot the machine

`- mkfs.ext3 /dev/hda5 --> format the new partition with a ext3 fs
- e2label /dev/hda5 /HOME --> label identical to the folder name we are moving, it can be anything
- mount LABEL=/HOME /mnt --> mount the new partition under /mnt
- cp -dpRv /home/* /mnt --> copy the whole /home folder, keeping symlinks and user rights
- umount /mnt --> unmount /mnt :-)`

vi /etc/fstab :
`add this line : LABEL=/HOME     /home     ext3      defaults        1 1
save and quit vi`

Finally, type :
`mount -a --> mount the partition under fstab
mount --> you should see the new partition mounted as /home`
