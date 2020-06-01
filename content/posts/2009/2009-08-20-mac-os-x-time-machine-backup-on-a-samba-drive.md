---
date: 2009-08-20
title: "Mac OS X - Time Machine backup on a Samba drive"
---







categories:
- Apple/Mac OS
- Howto


`Edit Jan 2010 : you can also run Time Machine backups on an AFP drive, which is faster (at least for me), see [http://blog.wains.be/post/afp-server-in-under-15-minutes-debian/](http://blog.wains.be/post/afp-server-in-under-15-minutes-debian/) for more info on installing Netatalk on your server.`

Time Machine, the built-in backup utility of OS X is nice (in a "run and forget about it" way) but has a few limitations.
The major problem is it will only backup your data to a physically attached drive (through USB or Firewire) by default. 

We will see how we can make it save your data to a Samba/Windows share (not that I like Samba, but OS X supports it well).

**(On OS X) In a terminal type this :**
`defaults write com.apple.systempreferences TMShowUnsupportedNetworkVolumes 1`

**(On the Samba server) Set up your share for Time Machine, here's an example you can add to your smb.conf :**
`[timemachine]
   path = /backup/timemachine
   comment = Time Machine backup
   browseable = yes
   writable = yes
   create mask = 0640
   directory mask = 0750
   valid users = user1
   hosts allow = 172.16.27.111`

**Type this at the CLI :**
`mkdir /backup/timemachine
chown user1. /backup/timemachine
chmod 750 /backup/timemachine`

**Stay logged as we will need to check something in a minute, type : **
`cd /backup/timemachine`

**(On OS X) Make sure you can access the newly created share (and write to it).**

**Go into System Preferences > Time Machine :**
Pick up the Samba share as destination
Wait for the first backup to start

**(On Samba server) As soon as the backup starts, you should check the content of /backup/timemachine** :
You should find a new directory named like this : 
`MacBook_002310d4c911.tmp.sparsebundle`
The name is following this pattern : 
`_.tmp.sparsebundle`
Write the name of the folder down, we will need that info in the next steps.

**(On OS X) After a coupe minutes you should get an error from Time Machine ** :
_**"Time Machine Error - The backup disk image could not be created." **_
This is supposed to happen, if it doesn't I still recommend you to follow the next steps, or your backup could misbehave after a while.

**Start the tool "Disk Utility".**
Click on "New Image"
Volume Name : Time Machine
Volume Size : whatever you want, I created a 100 GB volume (as I usually have around 50 GB or so on my machine). Do not worry, you don't need 100 GB available on your drive.
Volume Format : Mac OS Extended (Journaled)
Encryption : none
Partitions : Single partition - Apple Partition Map
Image Format : sparse bundle disk image

Save the bundle disk image on your desktop as "MacBook_002310d4c911.sparsebundle" (this is the name we wrote down two steps earlier **MINUS THE ".tmp" SECTION**)

**Copy that file to your Time Machine share on the samba server.**
My supposedly 100 GB file was actually taking 86 MB. The file (which actually is a directory) will grow as backups are performed.

**Now start Time Machine manually :**
This should not produce errors anymore. The first backup will obviously take some time.

Voilà !

**My suggestions :**
- you may not want Time Machine to run when you are over Wi-Fi. This is why I limited access to the Samba share to my wired IP address only (option "hosts allow").
- you can install [Time Machine Editor](http://timesoftware.free.fr/timemachineeditor/) which allows backup scheduling (fixing another lack of Time Machine) 
- Time Machine Editor also allows to disable automatic backups when the Time Machine drive is connected, I highly recommend doing so : as we are using a network drive here, every time you wake your computer up, OS X  automatically reconnects to the Samba share, and trigger Time Machine.

**Last word :**
- File Vault (built-in encryption system) and Time Machine do not get along very well, Google it up for details. 
- Source : [http://www.flokru.org/post/time-machine-backups-on-network-shares-in-leopard/](http://www.flokru.org/post/time-machine-backups-on-network-shares-in-leopard/) and Apple forums.
