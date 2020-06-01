---
date: 2007-03-23
title: "Quota on RHEL/CentOS"
---







categories:
- RHCE


Here are the steps to implementing quotas on a RedHat based system :

We will enable quotas on /home on the /dev/hda3 partition.



**Create user :**
`useradd user1
passwd user1`

**Edit /etc/fstab :**
From :
`/dev/hda3 /home ext3 defaults 1 2`
To : 
`/dev/hda3 /home ext3 defaults,usrquota,grpquota 1 2`

**Remount the disk (make sure it's not in use) :**
`mount -o remount /home`

**Check if usrquota and grpquota are enabled :**
`mount | grep /home`

**Create quota files :**
`quotacheck -cvug /home`

This creates /home/aquota.user and /home/aquota.group

**Check quota :**
`quotacheck -avug`

**Enable quota for user1 :**
`edquota user1`
Edit soft and hard limits (1000 = 1 MB) or inode values.

**Check the quota for user1 :**
`quota user1`

**Enable quota :**
`quotaon -avug`

In addition :

Through a cron, run everynight when the filesystem is not used :
`quotaoff -avug && quotacheck -avug && quotaon --avug`

Get quota stats :
`repquota -a`

**Warn users when their quota has been reached : **
`warnquota`

For some reason I had to manually edit /etc/quottab for warnquota to work
