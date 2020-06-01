---
date: 2007-01-26
title: "Make an ISO out of a CD"
---







categories:
- Howto
- Linux


`dd if=/dev/hda of=/home/user/cd.iso bs=2048 conv=notrunc`

Where /dev/hda is your cdrom drive..

How do I find that out ? (snippet)



`user@localhost:~$ mount
...
/dev/sda1 on /media/win type ntfs (rw)
**/dev/hda on /media/cdrom0 type iso9660 (ro,noexec,nosuid,nodev,user=user)**`

[The admin blogger](http://www.adminblogger.de/) mentionned an easier way :
`cat /dev/hda > /home/user/cd.iso`

More info : [http://www.linuxquestions.org/linux/answers/Applications_GUI_Multimedia/How_To_Do_Eveything_With_DD](http://www.linuxquestions.org/linux/answers/Applications_GUI_Multimedia/How_To_Do_Eveything_With_DD)
