---
date: 2013-06-21
title: "Repurposing a Barracuda Spam & Virus Firewall"
---







categories:
- Hardware
- Linux


I got my hands on a out of warranty/subscription/whatever Barracuda unit.

This unit is a Spam & Firewall 400 model from 2009 or something. Basically it's regular computer hardware in a 1U rack, with a Barracuda logo on it.

The mainboard is an MSI MS-7309, the CPU is an Athlon clocking at 2.7 GHz (VT available and enabled by default) and 2 GB of RAM. Storage is two drives of 250 GB in software RAID set up.

At boot, press DEL.

BIOS password was bcndk1. 

Enable boot from other device and remove the BIOS password if you wish. That's about it.

Grab your favorite Linux distribution. I picked the Debian 7.1.0 netinstall ISO and put it on a USB drive (sudo dd if=debian.iso of=/dev/sdb bs=1M)

I plugged it in, started the appliance and could proceed with the installation.

As you can see, I didn't even have to flash the BIOS or unlock anything.

Before you proceed with the installation of your favorite distribution, you may want to check the filesystem. It is not encrypted and you can get a look at what makes Barracuda Spam & Virus firewall. It's basically Postfix and probably Amavis and Clam, with some proprietary stuff put on top, sold as a black box.


References :

Mainboard specs : [http://www.cpu-world.com/CPUs/K10/AMD-Athlon%20II%20X2%20235e%20-%20AD235EHDK23GQ%20(AD235EHDGQBOX).html](http://www.cpu-world.com/CPUs/K10/AMD-Athlon%20II%20X2%20235e%20-%20AD235EHDK23GQ%20(AD235EHDGQBOX).html)

CPU specs : [http://www.msi.com/product/mb/K9N6SGM-V---K9N6PGM-FI---K9N6PGM-F.html](http://www.msi.com/product/mb/K9N6SGM-V---K9N6PGM-FI---K9N6PGM-F.html)

[http://blog.shiraj.com/2009/09/barracuda-spam-firewall-root-password/](http://blog.shiraj.com/2009/09/barracuda-spam-firewall-root-password/)

If that previous link disappears from the web, the possible passwords are :
BIOS PASSWORDS;
BIOS PW: 322232 32232 BCNDK1 ADMINBN99
DEFAULT PASSWORD (GUI) admin or adminbn99
RAID PASSWORD 0000

[http://arstechnica.com/civis/viewtopic.php?f=21&t;=1119778](http://arstechnica.com/civis/viewtopic.php?f=21&t=1119778)

