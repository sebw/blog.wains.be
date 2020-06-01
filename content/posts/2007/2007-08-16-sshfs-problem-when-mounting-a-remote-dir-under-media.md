---
date: 2007-08-16
title: "sshfs - problem when mounting a remote dir under /media"
---







categories:
- Linux


sshfs : [http://fuse.sourceforge.net/sshfs.html](http://fuse.sourceforge.net/sshfs.html)

(only tested under Ubuntu 7.04)

When trying to mount a remote directory under /media on the local filesystem, you'll get this :

`user@user:/media$ ls -l
total 8
?--------- ? ?    ?       ?                ? remote_dir
lrwxrwxrwx 1 root root    6 2007-04-24 11:29 cdrom -> cdrom0
drwxr-xr-x 2 root root 4096 2007-04-24 11:29 cdrom0`

Notice the question marks.. ?

No problem when mounting under, say /mnt :

`user@user:/mnt$ sshfs root@server:/path/to/remote/dir/ remote_dir/
user@user:/mnt$ ls -l
total 4
drwxr-xr-x 1 sw sw 4096 2007-08-16 15:19 remote_dir`


I filled a bug report : [https://bugs.launchpad.net/ubuntu/+source/sshfs-fuse/+bug/132936](https://bugs.launchpad.net/ubuntu/+source/sshfs-fuse/+bug/132936)
