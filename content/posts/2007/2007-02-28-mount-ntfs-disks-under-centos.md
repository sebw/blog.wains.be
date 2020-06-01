---
date: 2007-02-28
title: "Mount NTFS disks under CentOS"
---







categories:
- Red Hat/CentOS


Download the NTFS kernel modules from :
[http://www.linux-ntfs.org/content/view/135/71/](http://www.linux-ntfs.org/content/view/135/71/)

The download for the current kernel at the time is kernel-module-ntfs-2.6.9-42.0.8.EL-2.1.20-0.rr.10.0.i686.rpm

Install the RPM :
`rpm -ihv kernel-module-ntfs-2.6.9-42.0.8.EL-2.1.20-0.rr.10.0.i686.rpm`

Load the kernel module :
`modprobe ntfs`

Find the disk info : fdisk -l
One partition should be mentionned as HPFS/NTFS

Mount the partition :
`mkdir /media/win
mount -t ntfs /dev/hdb1 /media/win`

Voilà !
