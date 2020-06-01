---
date: 2011-08-04
title: "Spin down external USB drive on Debian Squeeze"
---







categories:
- Debian/Ubuntu
- Linux


It seems like I have at least two options to spin down my external USB drive used for [rsnapshot](http://www.rsnapshot.org) backups (Iomega 1TB). In the first place, I assumed it would spin down by itself by simply unmounting the volume, like on the Mac. But it doesn't.

So I gave sdparm a try :

    
    sdparm --command=stop /dev/backupdrive


It doesn't work :-)

I found a working solution at [http://forums.debian.net/viewtopic.php?f=7&t=60122](http://forums.debian.net/viewtopic.php?f=7&t=60122)

    
    sg_start --readonly --stop /dev/backupdrive


sg_start is part of sg3-utils package.
YMMV, I guess ?

Use the cmd_postexec option in rsnapshot to trigger the spin down.

Want the same device name for your external USB drive ? Check out [http://blog.wains.be/post/udev-always-the-same-device-name-for-your-usb-drives/](http://blog.wains.be/post/udev-always-the-same-device-name-for-your-usb-drives/)
