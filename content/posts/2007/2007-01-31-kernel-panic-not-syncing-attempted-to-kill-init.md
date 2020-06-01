---
date: 2007-01-31
title: "kernel panic - not syncing - Attempted to kill init"
---







categories:
- Howto
- Linux


My home server hard drive started to have problems (bad sectors, noisy, etc..)

I decided it was time to reinstall CentOS before it was too late

I had some important data on the old drive, I would recover the data by just pluging it when the new system would be installed on the new drive.



So, I installed CentOS 4.4 and partitioned the new drive pretty much like the old one..

This is my partitioning scheme :
`/boot /dev/hda1
/ /dev/hda2
/backup /dev/hda3
/tmp /dev/hda5
swap /dev/hda6`

After the installation, the system boots fine, so I decide to stop the machine, plug the old drive as primary slave and retrieve the data from it.

At reboot, I get some errors (mounted as read-only, already mounted, blah blah) then the system hangs with this message : "kernel panic - not syncing : Attempted to kill init"

Actually, the system tries to mount the partitions by calling their label, and not the device like they used to do in the past :

/etc/fstab by default looks like this :
`LABEL=/ / ext3 defaults 1 1`

The issue here is the system was finding two partitions named with the same label !

I rebooted the machine in runlevel 1 and edited /etc/fstab like this :
`/dev/hda2 / ext3 defaults 1 1`

I also had to edit /etc/grub.conf and change the "root=" argument from "root=LABEL=/" to "root=/dev/hda2"

Now, the system calls the device and boots fine, I can manually mount the old partitions and start retrieving my data.

I'll switch back to the label thing when the data have been transfered.

If anyone has a hint on why they prefer calling by the drive labels over devices, please drop me a comment..
