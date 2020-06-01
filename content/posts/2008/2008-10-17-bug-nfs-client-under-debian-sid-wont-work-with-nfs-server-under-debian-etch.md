---
date: 2008-10-17
title: "Bug - NFS client under Debian Sid wont work with NFS server under Debian"
---






  Etch'

categories:
- Debian/Ubuntu
- Linux


NFS clients under Debian Sid (unstable) are not able to browse NFS shares running on a Debian Etch server.

It is a bug in package nfs-common in Sid :
[http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=492970](http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=492970)

Clients running nfs-common 1.1.3 or above will not be able to access an NFS share running on server version 1.0.10.

You'd be able to mount the share just fine, but not browse the mounted directory :

`# cd serveur/
bash: cd: serveur/: Permission denied`

Downgrading to package [version 1.1.2-2](http://snapshot.debian.net/archive/post/debian/pool/main/n/nfs-utils/nfs-common_1.1.2-2_i386.deb) indeed fixed the problem.

Thanks to [Serge](http://www.ginsys.be/) for pointing me in the right direction !

