---
date: 2005-11-05
title: "Ubuntu Breezy (5.10) + Kismet + Orinoco patched drivers for monitoring and scan modes"
---

I installed a fresh copy of Ubuntu Breezy on my laptop
I had to recompile the orinoco drivers to get the monitoring capabilities of the card...

This is the how-to :

- Grab a copy of the sources here : [http://www.projectiwear.org/~plasmahh/orinoco.html](http://www.projectiwear.org/~plasmahh/orinoco.html). You should get the version 0.13 revision 8 if you run kernel 2.6.12
- Extract the files somewhere on the disk, let's say /tmp/orinoco
- Make a copy of the current orinoco and hermes modules in a safe place :
`mkdir /orinoco-backup
cp /lib/modules/2.6.12-x-x86/kernel/drivers/net/wireless/hermes* /orinoco-backup
cp /lib/modules/2.6.12-x-x86/kernel/drivers/net/wireless/orinoco* /orinoco-backup`

- get the package linux-source-2.6.xx, it will install a tar.gz file under /usr/src
- extract the linux source file under /usr/src/linux-source-2.6.xx

`cd /usr/src/
ln -s linux-source-2.6.xx linux
cp /tmp/orinoco/* /usr/src/linux/drivers/net/wireless/ (you can make a backup of the original source files if you feel the need)
cd /usr/src/linux
make menuconfig and remove everything but the wireless modules (thanks Julien ;))
make modules`
Depending on your hardware, it will take from minutes to hours...

Once the compilation has been processed :
- remove your orinoco card from the computer, this should unload the (old) orinoco modules
- then type :
`cp /usr/src/linux/drivers/net/wireless/hermes*.ko /lib/modules/2.6.12-x-x86/kernel/drivers/net/wireless/
cp /usr/src/linux/drivers/net/wireless/orinoco*.ko /lib/modules/2.6.12-x-x86/kernel/drivers/net/wireless/`

- Insert the orinoco card back in, it should load the new enhanced modules

You can always make a backup of the newly compiled drivers, indeed, after every kernel update, you'll have to copy over the default drivers with your custom drivers... hopefully, it should work for the next few upgrade of the kernel.. if not, you'll have to compile them again.
I usual keep the kernel version with the enhanced drivers and whenever I need to scan the neighborhood, I'll boot that kernel.

- get a copy of Kismet
- edit **/etc/kismet/kismet.conf**
Add the following line :
`source=orinoco,eth1,orinocosource`

Edit the other options to your needs, you can enable sound events (sox packages needed), anything regarding the client can be configured under kismet_ui.conf

How to test if scan mode works :
`root@host:~# iwlist eth1 scan
eth1      Scan completed :
Cell 01 - Address: 00:0x:xx:xx:xx:xx
ESSID:"xxx"
Mode:Managed
Frequency:2.442 GHz (Channel 7)
Quality:64/92  Signal level=-77 dBm  Noise level=-97 dBm
Encryption key:on`

The following tips were provided by Tony Arendt (www.arendt.org), thanks to him !

When leaving Kismet, your card could (*should*) hang :
Eject it or type :
`sudo iwpriv eth1 monitor 0 0
sudo iwpriv eth1 force_reset`

Tony successfully got drivers 0.15 working with monitor mode enabled by default.
Drivers are available here : [http://airsnort.shmoo.com/orinocoinfo.html](http://airsnort.shmoo.com/orinocoinfo.html)..
*YOU SHOULD* downgrade to firmware 7.52 to get the drivers working fine. Indeed, I stumbled upon unstable behavior with the drivers under firmware 8.52
