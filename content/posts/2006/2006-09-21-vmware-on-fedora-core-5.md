---
date: 2006-09-21
title: "VMware on Fedora Core 5"
---







categories:
- Linux


Source : [http://tredosoft.com/node/9](http://tredosoft.com/node/9)

I wasn't able to get VMware Workstation working under FC5 using the following guide.
It was painless to get it working under Ubuntu 6.06, see here : [http://blog.wains.be/?p=129](http://blog.wains.be/?p=129)



We want to install VMWare's free VMWare Player 1.0.1 build 19317 on Fedora Core 5. As far as I can tell this guide works with other VMWare products as well (eg. VMware Workstation/ ESX / GSX / Server etc.)

Fedora Core 5 comes with the 2.6.x Kernel which is not yet supported by VMWare. By default we shouldn't expect a new kernel to be supported out of the box.

my `uname -r` is 2.6.16-1.2080_FC5

Intalling

    * Switch to root
    * Download and install VMware Player from here

      Now running VMware Player would give you this message

[root@localhost ~]# vmplayer

vmware is installed, but it has not been (correctly)

configured for this system. To (re-)configure it,

invoke the following command: /usr/bin/vmare-config.pl.

[root@localhost ~]#

    * We need the kernel source (to build the vmmon modules). If you don't have it, install it by typing this in bash

yum install kernel-devel
Problems with vmware-config.pl

[You can skip this part if you want, it's here to show you what would happen if you run the vmware-config.pl without the patch below]

    * Run the config script

/usr/bin/vmware-config.pl

    * It will show you the EULA , you have to agree to it. Press Enter and space 3-4 times, type y then enter.
    * You will be asked about the location of "mime type icons", "desktop menu entry files",and where to install the application icon. I don't really care for these stuff so I press enter for defaults but if you do care give the script the proper locations.
    * If you continue, the script will give you the following message

None of the pre-built vmmon modules for VMware Player is
suitable for your running kernel. Do you want this
program to try to build the vmmon modules for your
system (you need to have a C compiler installed on your
system)?

Obviously we want to build the vmmon modules so we answer "y"

Now the script will ask you about the location of the C header files of your Kernel. Incase you are wondering the location is :

/lib/ modules/ 2.6.16-1.2080_FC5/ build/include

Even if you type the correct path you'll get the following message

The kernel defined by this directory of header files does
not have the same address space size as your running kernel.
Patching and reconfiguring

    * Download this script taken originally from here. Extract to a folder then run

./runme

    This script appears to be made by VMWare. I have no idea why it's not included by default. Answer "y" to all questions and it will start vmware-config.pl for you.

    * Same as above enter all info and answer "y" to all to start building the vmmon modules.

      If all goes well the script will start building the vmmon module. It will ask you if you want virtual networking, default ethernet adaptor, etc etc answer as you wish.
    * Finally you'll get the good-to-go message

      Starting VMware services:

Virtual machine monitor [ OK ]

Virtual ethernet [ OK ]

Bridged networking on /dev/vmnet0 [ OK ]

The configuration of VMware Player 1.0.1 build-19317 for
Linux for this kernel completed successfully. You can
now run VMware Player by invoking the following command:
"/usr/bin/vmplayer".

Enjoy,

--the VMware team
