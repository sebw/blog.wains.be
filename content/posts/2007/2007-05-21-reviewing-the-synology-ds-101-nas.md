---
date: 2007-05-21
title: "Reviewing the Synology DS-101 NAS"
---







categories:
- Hardware
- Misc
- NSLU2


I got my [DS-101](http://www.synology.com) (discontinued product) today and I'm so excited about it I want to tell the world ASAP :-D

The Synology DS-101 is a Network Attached Storage unit.
By default, it allows FTP and SMB connectivity.



It is running Linux and thus is easily hackable.
See : http://www.nslu2-linux.org/wiki/DS101/HomePage

Through some hacking, I was able to enable SSH. Now, I can even browse my files through SSH from Linux.

Thanks to the NSLU2 community for that.
Check this out, http://ipkg.nslu2-linux.org/feeds/optware/ds101/cross/stable/
They have build a lot of tools for the DS101. I just installed bind and may install some other stuff like SVN.

Upgrading to the latest firmware is a must.
It introduced these features (among others) for my box.

- Download Station : schedule BitTorrent/HTTP/FTP download, the NAS acts as a download center. You can download stuff overnight, without a single computer running. (see [link](http://www.synology.com/enu/products/features/downloadstation.php))
- PHP+MySQL : the NAS can now act as a web server
- UPS support : I plugged my MGE Protection Center 500 UPS to the NAS. It was detected instantly and I was able to set up the NAS to shutdown cleanly in case the power goes down. I was really missing that possibility with my actual linux server.
- ...

The unit boasts a USBCopy feature.
Basically, you plug your usb drive on the front USB port of the NAS. You press the "Copy" button next to the USB port, and the unit will copy the content of the usb drive to the hard drive.
Great feature, especially when you want to back up your stuff, are in a hurry and no computer is powered on (yeah I shutdown the computers every night).

I haven't tested the read/write perfs but that is not the biggest issue to me.
My main goal was to get rid of my linux server which was basically only a file server. But I still wanted to be able to securely reach my files from anywhere in the world, I found out it was possible to get SSH on a Synology box..
I'm saving room but also energy now, that's definitely a great thing.
