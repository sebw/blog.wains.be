---
date: 2008-01-21
title: "Ubuntu on Dell XPS M1330"
---







categories:
- Hardware
- Linux


**Ubuntu 7.10 Desktop Edition**

**Laptop specs :**
Dell XPS M1330
WLED screen
Webcam 0.3 Mpixels
Fingerprint scanner
Media Card Reader Ricoh
Wireless Intel iwl4965 AGN
Cpu : T7500 2.2 Ghz
Memory : 3 GB RAM 667 Mhz
HDD : Seagate 200 GB 7200 RPM
Sound : SigmaTel STAC9228

**lspci :**
00:00.0 Host bridge: Intel Corporation Mobile PM965/GM965/GL960 Memory Controller Hub (rev 0c)
00:01.0 PCI bridge: Intel Corporation Mobile PM965/GM965/GL960 PCI Express Root Port (rev 0c)
00:1a.0 USB Controller: Intel Corporation 82801H (ICH8 Family) USB UHCI Contoller #4 (rev 02)
00:1a.1 USB Controller: Intel Corporation 82801H (ICH8 Family) USB UHCI Controller #5 (rev 02)
00:1a.7 USB Controller: Intel Corporation 82801H (ICH8 Family) USB2 EHCI Controller #2 (rev 02)
00:1b.0 Audio device: Intel Corporation 82801H (ICH8 Family) HD Audio Controller (rev 02)
00:1c.0 PCI bridge: Intel Corporation 82801H (ICH8 Family) PCI Express Port 1 (rev 02)
00:1c.1 PCI bridge: Intel Corporation 82801H (ICH8 Family) PCI Express Port 2 (rev 02)
00:1c.3 PCI bridge: Intel Corporation 82801H (ICH8 Family) PCI Express Port 4 (rev 02)
00:1c.5 PCI bridge: Intel Corporation 82801H (ICH8 Family) PCI Express Port 6 (rev 02)
00:1d.0 USB Controller: Intel Corporation 82801H (ICH8 Family) USB UHCI Controller #1 (rev 02)
00:1d.1 USB Controller: Intel Corporation 82801H (ICH8 Family) USB UHCI Controller #2 (rev 02)
00:1d.2 USB Controller: Intel Corporation 82801H (ICH8 Family) USB UHCI Controller #3 (rev 02)
00:1d.7 USB Controller: Intel Corporation 82801H (ICH8 Family) USB2 EHCI Controller #1 (rev 02)
00:1e.0 PCI bridge: Intel Corporation 82801 Mobile PCI Bridge (rev f2)
00:1f.0 ISA bridge: Intel Corporation 82801HEM (ICH8M) LPC Interface Controller (rev 02)
00:1f.1 IDE interface: Intel Corporation 82801HBM/HEM (ICH8M/ICH8M-E) IDE Controller (rev 02)
00:1f.2 SATA controller: Intel Corporation 82801HBM/HEM (ICH8M/ICH8M-E) SATA AHCI Controller (rev 02)
00:1f.3 SMBus: Intel Corporation 82801H (ICH8 Family) SMBus Controller (rev 02)
01:00.0 VGA compatible controller: nVidia Corporation GeForce 8400M GS (rev a1)
03:01.0 FireWire (IEEE 1394): Ricoh Co Ltd R5C832 IEEE 1394 Controller (rev 05)
03:01.1 Generic system peripheral [0805]: Ricoh Co Ltd R5C822 SD/SDIO/MMC/MS/MSPro Host Adapter (rev 22)
03:01.2 System peripheral: Ricoh Co Ltd R5C843 MMC Host Controller (rev 12)
03:01.3 System peripheral: Ricoh Co Ltd R5C592 Memory Stick Bus Host Adapter (rev 12)
03:01.4 System peripheral: Ricoh Co Ltd xD-Picture Card Controller (rev 12)
09:00.0 Ethernet controller: Broadcom Corporation NetLink BCM5906M Fast Ethernet PCI Express (rev 02)
0c:00.0 Network controller: Intel Corporation PRO/Wireless 4965 AG or AGN Network Connection (rev 61)

**lsusb :**
Bus 007 Device 004: ID 05a9:7670 OmniVision Technologies, Inc. 
Bus 007 Device 001: ID 0000:0000  
Bus 001 Device 001: ID 0000:0000  
Bus 004 Device 002: ID 045e:007d Microsoft Corp. Notebook Optical Mouse
Bus 004 Device 001: ID 0000:0000  
Bus 006 Device 002: ID 0483:2016 SGS Thomson Microelectronics Fingerprint Reader
Bus 006 Device 001: ID 0000:0000  
Bus 005 Device 001: ID 0000:0000  
Bus 003 Device 001: ID 0000:0000  
Bus 002 Device 005: ID 0a5c:4503 Broadcom Corp. 
Bus 002 Device 004: ID 0a5c:4502 Broadcom Corp. 
Bus 002 Device 003: ID 413c:8126 Dell Computer Corp. 
Bus 002 Device 002: ID 0a5c:4500 Broadcom Corp. 
Bus 002 Device 001: ID 0000:0000  


**webcam :** works out of the box (if not, see links)

**sound :** microphone doesn't work out of the box

1. Enable gutsy-proposed repository
2. Install linux-backports-modules-2.6.22-14-generic
3. Disable gutsy-proposed repository

In sound preferences, enable every channels, in the option tab select "Digital Mic 1" as Digital Input Source

I set my hardware volume keys to control the "front" channel (don't forget to set the tray volume icon to display the front volume as well : right click, preferences).

2nd front headset jack isn't working properly, see [http://linux.dell.com/wiki/index.php/Ubuntu_7.10/Issues/Second_Headphone_Jack_Does_Not_Work](http://linux.dell.com/wiki/index.php/Ubuntu_7.10/Issues/Second_Headphone_Jack_Does_Not_Work)


**Temperature probes :** install sensors-applet, the ACPI CPU sensor is available out of the box

Install hddtemp and make it run at boot.. sensors-applet will then be able to monitor the HDD sensor.

Other sensors can be used :
• with i8kutils and "modprobe i8k force=1"
• with coretemp, "modprobe coretemp" and cat /sys/devices/platform/coretemp.?/temp1_input 


sensors-applet doesn't support nvidia probes in Ubuntu 7.10

[http://aldeby.wordpress.com/post/compiling-gnome-sensors-applet-with-nvidia-support/](http://aldeby.wordpress.com/post/compiling-gnome-sensors-applet-with-nvidia-support/)

I've built sensors-applet with nvidia support for i386 : [http://blog.wains.be/pub/sensors-applet_2.2.1-1_i386.deb](http://blog.wains.be/pub/sensors-applet_2.2.1-1_i386.deb)

[![Image Hosted by ImageShack.us](https://blog.wains.be/images/00-imageshack.jpg)  

_After installing sensors-applet with nvidia support (CPU, HDD, nvidia)_

With the default nvidia restricted drivers installed by ubuntu, the sensor said the graphic card is always around 68-70°C

After installing version 169.07 using envy, the sensor reports values anywhere between 52°C and 60°C.. not sure if it's the old drivers reporting the wrong value or anything.. can't hear a difference from the fan.


**Screen :** the monitor dims automatically when not using the computer for 1 minute (apparently a problem with gnome power saving thing, if I set the brightness to 100 % and access Gnome Power settings, the monitor dims immediately).


**Hibernation : **After hibernation, CPU cores are always running at 2.2 Ghz (there's a fix, see links)

**Dell keyboard shortcuts :** working. I configured the dell "home" key to open my home directory in nautilus (gnome keyboard settings)

**Wireless : **works out of the box
Wifi Catcher apparently doesn't work when Ubuntu is installed

Wifi LED doesn't turn on under Linux (Bluetooth does)

**Fingerprint scanner :** can work with thinkfinger (see links)

**Card Reader :** SD works out of the box, Memory stick doesn't, other media not tested


**Useful links :**

[https://wiki.ubuntu.com/InstallingUbuntuOnADellXPSM1330](http://https://wiki.ubuntu.com/InstallingUbuntuOnADellXPSM1330)
[http://intr.overt.org/blog/?page_id=56](http://intr.overt.org/blog/?page_id=56)
[http://doc.ubuntu-fr.org/dell_xps_m1330](http://doc.ubuntu-fr.org/dell_xps_m1330)
[http://doc.ubuntu-fr.org/thinkfinger](http://doc.ubuntu-fr.org/thinkfinger)
[http://blog.higherthings.org/borghardt/article/3077.html](http://blog.higherthings.org/borghardt/article/3077.html)
[https://wiki.ubuntu.com/ThinkFinger](https://wiki.ubuntu.com/ThinkFinger)
[http://www.atlas95.com/blog/tag/xps-m1330/](http://www.atlas95.com/blog/tag/xps-m1330/)
[http://linux.dell.com/wiki/index.php/Ubuntu_7.10#Dell_OS_Reinstallation_7.10_DVD_ISO](http://linux.dell.com/wiki/index.php/Ubuntu_7.10#Dell_OS_Reinstallation_7.10_DVD_ISO)
