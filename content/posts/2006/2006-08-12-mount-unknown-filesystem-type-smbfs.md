---
date: 2006-08-12
title: "mount- unknown filesystem type smbfs"
---







categories:
- Linux


While trying to mount a samba share under Fedora Core 5, I got this message : "mount: unknown filesystem type 'smbfs'"

The command I was trying was "mount -t smbfs -o username=user //server/share /mnt/dir"

"smbfs" has been replaced by "cifs"

Now, you need to use : "mount -t cifs -o username=user //server/share /mnt/dir"
