---
date: 2005-11-03
title: "Kernel compilation under CentOS"
---

The package kernel-sources is needed

You need to make sur /usr/src/linux is a symlink pointing to the kernel sources directory

Example :
`lrwxrwxrwx    1 root     root            9 Jul  2 13:57 linux -> linux-2.4
lrwxrwxrwx    1 root     root           18 Sep 29 18:57 linux-2.4 -> linux-2.4.21-37.EL
drwxr-xr-x   16 root     root         4096 Nov  2 15:33 linux-2.4.21-37.EL`



If not :
`cd /usr/src
ln -s linux-2.4 linux`

Then :
`cd /usr/include
for link in asm linux scsi ; do mv $link $link-old ; done --> should avoid error when compiling the kernel
ln -s /usr/src/linux/include/asm-i386 asm --> this link is needed for a proper compilation
ln -s /usr/src/linux/include/linux linux --> dito
ln -s /usr/src/linux/include/scsi scsi --> dito
cd /usr/src/linux
cp /usr/src/linux/configs/kernel-2.4.21-i686.config .config --> copy the old or current kernel config file`
- you may need to issue a "make clean" and "make mrproper" if you already tried to compile and got errors
- you may need to issue a "make oldconfig" --> to make it just prompt on unanswered question in the config file
- vi Makefile, edit the variable EXTRAVERSION, set it to anything.. e.g.: your name

`make menuconfig`
- edit the kernel config to your needs

`make bzImage --> if errors "ln -s /usr/src/linux/include/asm-generic /usr/include/asm-generic"
make modules
make modules_install
make install`

We make a backup of our custom config file :
`cp /usr/src/linux/.boot /boot/config-2.x.x-EXTRAVERSION `

The new kernel is located under /boot and the configuration of grub should be updated, verify the default kernel.

More info : [http://lea-linux.org/cached/index/Kernel-kernel.html](http://lea-linux.org/cached/index/Kernel-kernel.html)
