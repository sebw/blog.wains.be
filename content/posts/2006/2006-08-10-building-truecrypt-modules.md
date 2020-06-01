---
date: 2006-08-10
title: "Building truecrypt modules"
---







categories:
- Howto
- Linux


You may need to rebuild the truecrypt module anytime there is a kernel update...

1. yum install kernel(-smp)-devel
2. wget http://download.fedora.redhat.com/pub/fedora/linux/core/updates/5/SRPMS/kernel-2.6.1(VERSION)_FC5.src.rpm

Optional for SMP :
Edit /usr/src/redhat/SPECS/kernel-2.6.spec
`%define buildsmp 1`

3. rpmbuild -bp --target=$(arch) kernel-2.6.spec (I use i686 as arch)
4. when done : cd BUILD/kernel-2.6.xx/linux-2.6.xx
5. cp configs/kernel-2.6.xx-$(arch)-(smp).config .config

This is a list of available config files :
`[root@localhost configs]# ll
total 544
-rw-r--r-- 1 root root 61336 Aug 14 20:45 kernel-2.6.17-i586.config
-rw-r--r-- 1 root root 58562 Aug 14 20:45 kernel-2.6.17-i586-smp.config
-rw-r--r-- 1 root root 65979 Aug 14 20:46 kernel-2.6.17-i686.config
-rw-r--r-- 1 root root 66038 Aug 14 20:46 kernel-2.6.17-i686-kdump.config
-rw-r--r-- 1 root root 65997 Aug 14 20:46 kernel-2.6.17-i686-smp.config
-rw-r--r-- 1 root root 61792 Aug 14 20:46 kernel-2.6.17-i686-xen0.config
-rw-r--r-- 1 root root 61840 Aug 14 20:46 kernel-2.6.17-i686-xen.config
-rw-r--r-- 1 root root 29296 Aug 14 20:46 kernel-2.6.17-i686-xenU.config`

6. vi Makefile

Edit the line EXTRAVERSION to match your system :

`EXTRAVERSION = -1.2157_FC5smp`

7. make oldconfig

8. when done : mv /usr/src/redhat/BUILD/kernel-2.6.VERSION/linux-2.6.VERSION.i686 /usr/src/kernels
9. ln -s /usr/src/kernels/kernel-2.6.VERSION /usr/src/linux
10. grab and unpack truecrypt sources
11. execute ./Linux/build.sh (this will take some time)
12. when done : cp ./Kernel/truecrypt.ko /usr/share/truecrypt/kernel/truecrypt-2.6.VERSION.ko

You're done.
