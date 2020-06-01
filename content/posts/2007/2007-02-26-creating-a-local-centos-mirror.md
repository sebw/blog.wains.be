---
date: 2007-02-26
title: "Creating a local CentOS mirror"
---







categories:
- RHCE


For my RHCE prep, I'm setting up a small lab of 3 machines.. one machine will act as the server..
For my ease of use, I decided to build a local mirror of the CentOS repository (base and updates).

I had downloaded the 4 ISO images of CentOS 4.4 overnight and burnt them.

Please consider the following :
I'm only building a local copy of the base and updates repo for CentOS 4.4 for the i386 architecture.



**Follow these steps :**

`mkdir -p /var/www/html/mirror/centos/4.4/os/i386/
ln -s /var/www/html/mirror/centos/4.4 /var/www/html/mirror/centos/4`

Insert CD1
`cp -a /media/cdrom/* /var/www/html/mirror/4.4/os/i386/`
When done
`eject /dev/cdrom`

Repeat the previous steps for CD2, CD3 and CD4 (say yes when asking for overwriting)

When done, you should make sure your base repository is up to date
`rsync -avzH --delete eu-msync.centos.org::CentOS/4.4/os/i386/ /var/www/html/mirror/centos/4.4/os/i386/`

Then, synchronize the "updates" repository, this should take a while and will download a massive amount of data (around 6.3 Gb when I built the mirror)
`mkdir -p /var/www/html/mirror/centos/4.4/updates/i386/
rsync -avzH --delete eu-msync.centos.org::CentOS/4.4/updates/i386/ /var/www/html/mirror/centos/4.4/updates/i386/`

**Make the mirror available through NFS (no security consideration here ! quick and easy way) :**
vi /etc/exports :
`/var/www/html/mirror/centos/4/os/i386 *(rw,sync)`

Start NFS :
`service nfs start
chkconfig nfs on`

Server : server
Path : /var/www/html/mirror/centos/4/os/i386/

**Making the mirror available through HTTP :**
`service httpd start
chkconfig httpd on`

Path : http://server/mirror/centos/4/os/i386/


**Please note :
If you do use the CentOS rsync service, it is implied that you are providing a mirror for public use and give the CentOS admins authority to publicise the mirror**

If you use the rsync service to build a local mirror for testing/learning purpose, please just synchronize it once. DON'T ABUSE.
