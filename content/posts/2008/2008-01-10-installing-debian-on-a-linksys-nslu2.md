---
date: 2008-01-10
title: "Installing Debian on a Linksys NSLU2"
---







categories:
- Debian/Ubuntu
- Hardware
- Linux
- NSLU2


1. Grab firmware : [http://www.slug-firmware.net/d-dls.php](http://www.slug-firmware.net/d-dls.php)

2. (optional) Install UpSlug2 on your computer : [http://www.nslu2-linux.org/wiki/Main/UpSlug2](http://www.nslu2-linux.org/wiki/Main/UpSlug2)

UpSlug2 is a tool to flash your NSLU2 from a computer on the same network.

This is required if you are reinstalling an already Debianized NSLU2, otherwise you can use the web management on a new unit.

3. Install Debian : [http://www.cyrius.com/debian/nslu2/](http://www.cyrius.com/debian/nslu2/)

In order for the install to complete as fast as possible, unselect any package group (even base).. took less than 1 hour to complete that way.

**When done (optional) :**

4. Reducing memory usage : [http://www.cyrius.com/debian/nslu2/reducing-memory.html](http://www.cyrius.com/debian/nslu2/reducing-memory.html)

`/etc/inittab and comment "T0:23:respawn:/sbin/getty -L ttyS0 115200 linux"`

5. Follow the useful hints from (scroll halfway down) : [http://svn.debian.org/wsvn/pkg-nslu2-utils/trunk/debian/README.Debian?op=file](http://svn.debian.org/wsvn/pkg-nslu2-utils/trunk/debian/README.Debian?op=file)

`/etc/default/rcS and set FSCKFIX=yes
/etc/default/bootlogd and enable bootlog`

6. In case you're running the OS from a USB drive (limited write cycles) : [http://www.nslu2-linux.org/wiki/HowTo/UseAMemoryStickAsMainDrive](http://www.nslu2-linux.org/wiki/HowTo/UseAMemoryStickAsMainDrive)

`[disable MARK logs](http://blog.wains.be/post/debian-etch-disable-the-mark-log/) : /etc/defaults/syslogd and set SYSLOGD="-m 0"
mount / with noatime : /etc/fstab and add option noatime for the / mount point`

7. In case the NSLU2 uses a static IP

`apt-get remove dhcp*`

8. Generate UTF-8 locales

Accents in the filesystem are not displayed correctly (while OK when accessing files remotely)

`apt-get install locales
dpkg-reconfigure locales`

Select an UTF-8 locales to fix the problem (had to reboot for changes to be taken in account, not sure if there's a way to avoid rebooting)

9. Emit 3 beeps when NSLU2 has started

apt-get install beep

Edit /etc/rc.local and add :
/usr/bin/leds beep; sleep 1; /usr/bin/leds beep; sleep 1; /usr/bin/leds beep
