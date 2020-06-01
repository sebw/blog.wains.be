---
date: 2007-03-28
title: "Install the latest kernel from Kernel.org under CentOS 4.4"
---







categories:
- Linux
- Red Hat/CentOS


This was just part of an experiment on a test machine.
I wanted to see if it was a pain to install the latest kernel to a CentOS 4.4 system, and see if the system was stable etc.

The following steps are based on [http://howtoforge.com/kernel_compilation_centos](http://howtoforge.com/kernel_compilation_centos) guide.

It is probably not the best way to deal with it. And you probably don't want to install the latest kernel on a production system.
The kernel provided by the CentOS team is just FINE for 99 % cases.

USE AT YOUR OWN RISK.



`wget http://www.kernel.org/pub/linux/kernel/v2.6/linux-2.6.20.4.tar.bz2 -O /usr/src/linux-2.6.20.4.tar.bz2
cd /usr/src
tar xjvf linux-2.6.20.4.tar.bz2
ln -s linux-2.6.40 linux
cd linux
make clean && make mrproper
cp /boot/config`uname -r` ./.config
make oldconfig
make all
make module_install
make install`

Reboot, my system was booting and seemed stable.. Woaw. :)


