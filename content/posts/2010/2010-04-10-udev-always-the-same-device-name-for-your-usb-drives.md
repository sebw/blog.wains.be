---
date: 2010-04-10
title: "Udev - always the same device name for your USB drives"
---







categories:
- Linux


_Tested on Debian Lenny_

This is how your USB drive will get the same device name (/dev/sdXX or whatever) no matter when or which USB port you plug it in.

`# udevinfo -a -p $(udevinfo -q path -n /dev/sdd1)`

Where /dev/sdd1 is the partition you want to always get the same device name.

You'll get a bunch of output, but this is the interesting part : 

`  looking at parent device '/devices/pci0000:00/0000:00:1d.7/usb5/5-2':
    KERNELS=="5-2"
    **SUBSYSTEMS=="usb"**
    DRIVERS=="usb"
    ATTRS{configuration}=="USB Mass Storage"
    ATTRS{bNumInterfaces}==" 1"
    ATTRS{bConfigurationValue}=="1"
    ATTRS{bmAttributes}=="c0"
    ATTRS{bMaxPower}=="  2mA"
    ATTRS{urbnum}=="466"
    ATTRS{idVendor}=="059b"
    ATTRS{idProduct}=="0370"
    ATTRS{bcdDevice}=="0000"
    ATTRS{bDeviceClass}=="00"
    ATTRS{bDeviceSubClass}=="00"
    ATTRS{bDeviceProtocol}=="00"
    ATTRS{bNumConfigurations}=="1"
    ATTRS{bMaxPacketSize0}=="64"
    ATTRS{speed}=="480"
    ATTRS{busnum}=="5"
    ATTRS{devnum}=="5"
    ATTRS{version}==" 2.00"
    ATTRS{maxchild}=="0"
    ATTRS{quirks}=="0x0"
    ATTRS{authorized}=="1"
    ATTRS{manufacturer}=="Iomega"
    ATTRS{product}=="External HD"
    **ATTRS{serial}=="9E1718FFFFFF"**`

In bold, the interesting part for our udev rule.

Edit /etc/udev/rules.d/10-local.rules (10-* should come before any other file) :

`SUBSYSTEMS=="usb", ATTRS{serial}=="9E1718FFFFFF", KERNEL=="sd?1", NAME="backupdrive"`

This rule will match our partition, usually detected by default as sdc1 or sdd1, thus the rule KERNEL=="sd?1".

NAME will be the name of our device : /dev/backupdrive

Restart udev :

`# /etc/init.d/udev restart`

Plug your drive again, and you should see /dev/backupdrive (while still appearing in dmesg output as sdX1)

`# ls -l  /dev/backupdrive 
brw-rw---- 1 root floppy 8, 33 2010-04-10 22:33 /dev/backupdrive`

Source : [http://ubuntuforums.org/showthread.php?t=168221](http://ubuntuforums.org/showthread.php?t=168221)
