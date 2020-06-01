---
date: 2009-06-30
title: "GNBD on Debian installation howto"
---







categories:
- Debian/Ubuntu
- Howto
- Linux


[GNBD](http://sources.redhat.com/cluster/gnbd/) is some kind of alternative to iSCSI and to a (much) lower extent to sshfs or other filesharing systems (NFS, Samba, etc.).

Unlike iSCSI, there's no authentication or such, I invite you to read the documentation to learn more about GNBD.

**Server (server.example.org) :**
 
_Optional : Create a test filesystem_
 
`dd if=/dev/zero of=/home/disk bs=1M count=1000
losetup -f (find the next available loop device)
losetup /dev/loop0 /home/disk
mkfs.ext3 -j /dev/loop0`
 
 
_Install GNBD on the server and start :_
 
`apt-get install gnbd-server
/etc/init.d/gnbd-server start or gnbd-server -n`
 
 
_Export the device :_
 
`gnbd_export -d /dev/loop0 -e DISK -c`
 
Replace /dev/loop0 by any device on your system you want to share (eg : /dev/sda2).
DISK will be the name of the exported device.
 
_List the current exported drives :_
 
`gnbd_export`
 
 
**Client**

_Install GNBD on the client (client.example.org) :_
 
`apt-get install gnbd-client redhat-cluster-modules-2.6.26-2-686`
(pick the package matching your architecture)
 
`modprobe gnbd`
 
 
_List exports on the server from the client :_
 
`gnbd_import -e server.example.org -n`
 
 
_Create the device for the shared drive on the client :_
 
`gnbd_import -i server.example.org -n`
 
This will result in a new device available under /dev/gnbd/DISK
 
 
_Mount the device :_
 
`mount /dev/gnbd/DISK /mnt/DISK`

Done !


**Sources :**

[Linux Magazine France](http://www.gnulinuxmag.com/) n° [118](http://ed-diamond.com/feuille_lmag118/index.html) and [Vincent](http://www.404blog.net/) ;-)
