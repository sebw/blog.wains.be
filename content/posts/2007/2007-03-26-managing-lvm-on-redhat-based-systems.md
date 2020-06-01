---
date: 2007-03-26
title: "Managing LVM on Red Hat based systems"
---







categories:
- RHCE


**Steps :**
1. Creation of Physical Volumes (container of volume groups)
2. Creation of Volume Groups (container of logical volumes)
3. Creation of Logical Volumes ("partitions")
4. Formatting the Logical Volumes
5. (optional) Resizing Logical Volumes



**1. Creation of Physical Volumes (PV)**

`pvcreate /dev/hda4
pvdisplay`

It is good practice to always use "(pv|vg|lv)display" after creating a volume.

/dev/hda4 is an empty partition.. see fdisk manpages for help.

**2. Creation of Volume Groups (VG)**

`vgcreate VolGroup00 /dev/hda4
vgdisplay`

We are naming the volume group "VolGroup00", call it whatever you like.
The VG will take the whole partition space here. You can specify the size by using the "-s" option.

**3. Creation of Logical Volumes (LV)**

`lvcreate --size 500M --name LogVol00 VolGroup00
lvdisplay`

A 500M Logical Volume has been creating inside "VolGroup00".

**4. Formatting the newly created Logical Volume**

`mkfs.ext3 /dev/VolGroup00/LogVol00
mount /dev/VolGroup00/LogVol00 /somedir`

**5. (optional) Resizing a Logical Volume**

`umount /somedir
lvextend -v -L +100M /dev/VolGroup00/LogVol00
e2fsck -f /dev/VolGroup00/LogVol00
resize2fs /dev/VolGroup00/LogVol00
mount /dev/VolGroup00/LogVol00 /somedir`

We made the Logical Volume 100M bigger.
Then, you need to resize the ext3 partition inside the Logical Volume.
