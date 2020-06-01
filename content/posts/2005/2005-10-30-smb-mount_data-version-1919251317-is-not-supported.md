---
date: 2005-10-30
title: "SMB - mount_data version 1919251317 is not supported"
---

Under a fresh install of Ubuntu 5.10, I had an error when trying to mount a SMB share on my CentOS 3.5 server :

`user@host:/media$ sudo mount -t smbfs //server/path /media/path -o rw,username=username,password=password
mount: wrong fs type, bad option, bad superblock on //server/path,
missing codepage or other error
In some cases useful info is found in syslog - try
dmesg | tail  or so`

Let's see what the logs say :

`user@host:/media$ dmesg | tail
4324521.077000 smbfs: mount_data version 1919251317 is not supported`

**I just needed the missing "smbfs" package** :)

How to install the package on the most popular systems :
`RHEL based systems : yum install smbfs
Debian based systems : apt-get install smbfs`

After installing smbfs :
`user@host:/media$ sudo mount -t smbfs //server/path /media/path -o rw,username=username,password=password
user@host:/media$`

Voila  !
Like this article ? Support this site by visiting the partners in the sidebar !
