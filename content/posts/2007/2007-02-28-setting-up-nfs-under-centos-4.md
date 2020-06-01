---
date: 2007-02-28
title: "Setting up NFS + autofs under CentOS 4"
---







categories:
- RHCE


Packages needed : nfs-utils



**Server side**

Edit /etc/exports :
`/home *(rw,sync)`

Start service and make sure it'll start at boot
`service nfs start
chkconfig nfs on`

**Client side**

Check if you can reach the server :
`rpcinfo -p 10.0.0.254`

Manually mounting the shared folder
`mount -t nfs 10.0.0.254:/home /home`

**Setting up autofs on the client side to automount the NFS share :**

Package needed : autofs

Let's say we have a user "admin"

**Edit /etc/auto.master :**
`/home /etc/auto.misc --timeout 60`

**Edit /etc/auto.misc :**
`admin -rw,soft,rsize=8192,wsize=8192 server:/home/admin`

**Start the service :**
`service autofs start`

It should mount the NFS share.

**Check the status :**
`service autofs status`



