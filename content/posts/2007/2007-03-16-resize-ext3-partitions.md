---
date: 2007-03-16
title: "Resize ext3 partitions"
---







categories:
- Howto
- Linux


Say we have a brand new already ext3 formatted disk on our system.. say it has a 300 GB partition and it is brand new, so not running anything on the system. DO NOT TRY RESIZING PARTITIONS ON SYSTEM DISKS UNLESS YOU KNOW WHAT YOU ARE DOING.



**We are gonna copy the old 100 GB partition to the new partition.**

Old : sda2
New : sdb1

`dd if=/dev/sda2 of=/dev/sdb1 bs=4k`

When it's done, if mounted, the system will think the new partition is 100 GB big. We need to resize it.

Don't mount ! Or if you did to make sure data were correctly copied, unmount the new partition !

**Check for problems on the new partition**

`fsck -n /dev/sdb1`

**Remove the old ext3 journal, this is needed to resize..**

`tune2fs -O ^has_journal /dev/sdb1`

**Force checking, this is absolutely needed for the next step**

`e2fsck -f /dev/sdb1`

**Resize partition**

`resize2fs /dev/sdb1`

**Check fs again**

`fsck -n /dev/sdb1`

**Create ext3 journal**

`tune2fs -j /dev/sdb1`

Now mount the new partition, relabel it or edit your fstab.. It all depends on your config there.

More info : [http://www.howtoforge.com/linux_resizing_ext3_partitions](http://www.howtoforge.com/linux_resizing_ext3_partitions)

Wouter explains you can do online resizing of an ext3 partition : [http://www.grep.be/blog/en/computer/cluebat/actually_you_can](http://www.grep.be/blog/en/computer/cluebat/actually_you_can)
