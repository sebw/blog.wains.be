---
date: 2010-07-08
title: "HTC Hero under Android 2.1 + root + tethering + OpenVPN"
---







categories:
- Android
- VPN


So I finally jumped into the wonderful world of custom ROM, with my HTC Hero (unbranded German unit).

This phone was very slow and frustrating with the stock 1.5 ROM. I was planning on getting the Nexus One while being in the US in late June, but finally preferred giving the custom ROM option a try. I think I was right, this saved me a couple hundred dollars, and the phone is amazingly fast now.

**ALRIGHT : I'M NOT RESPONSIBLE FOR ANY BRICKED PHONE. OK ? GOOD.**

I followed this first guide : [http://sites.google.com/site/roothtchero/](http://sites.google.com/site/roothtchero/)

It may seem complicated and confusing but really, it's not.
Basically you flash the recovery image with flashrec, something you should only do once. Actually this is the sensitive operation of the whole process. If you mess up there (like pulling the battery while flashing, if you really want to give it a try be quick it takes like 2 seconds for the flash to be complete), the phone officially becomes a paperweight.
You'll be able to access the recovery mode by pressing the HOME KEY + HANG UP KEY when turning the phone on.
From there, you can do a lot of operations on your phone.
Nandroid backup will backup the phone as is, including the OS, radio firmware, apps and settings. Everything will be stored on the SD card. My first backup (Android 1.5) weighs around 200 MB.
It is highly recommended to backup your phone anytime you want to try a new ROM.

You can also mount the SD card on your computer while in the recovery mode (with the USB-MS option).

You load a custom ROM onto the root of the SD card, you select the "flash/restore" menu, pick up the ROM you want and hit confirm. Actually, I recommend wiping the phone before loading a ROM.

After a few minutes it should say the install is done. You can reboot and enjoy your new ROM.

The process took less than 15 minutes for me.

Talking about the ROM, I went with [VillainROM 12.0 ROM](http://www.villainrom.co.uk) (after testing a few others). It is an Android 2.1 custom ROM with Sense. I've been using it for a couple of days, it's very stable and fast. Hardware is working perfectly.
This ROM comes pre-rooted. It already comes with OpenVPN binaries and tun.ko module and aNetShare for Wi-Fi tethering. Neat !

Actually, I only had to install "OpenVPN Settings" from the market to get me going.

You put all your config files, keys, etc. under a folder named "openvpn" on the SD card.
Config files must end with *.ovpn !

In OpenVPN Settings, you'll have to load the tun modules when OpenVPN starts.
I had to use the insmod method.
Specify the path to the module : /system/lib/modules/tun.ko

Next, you'll have to grab the Android SDK on your computer.
When it's done, plug your phone into your computer with the USB cable, and type this as root (assuming your are under Linux or Mac) :
`/home/user/android/adb shell`
You should have a prompt on your phone now.

Type :
`su
bash`

You should have a bash prompt now :-)

Now type the following :

`cd /system/xbin/bb
mount -o remount,rw /dev/block/mtdblock3 /system
ln -s ../ifconfig
ln -s ../route
mount -o remount,ro /dev/block/mtdblock3 /system`

This is what I had to do because OpenVPN was complaining about ifconfig and route commands. Apparently OpenVPN looks for the binaries under /system/xbin/bb. Those are under /system/xbin/.

I got the explanation from this link : [http://www.villainrom.co.uk/viewtopic.php?f=46&t=1888](http://www.villainrom.co.uk/viewtopic.php?f=46&t=1888)

Exit the shell, unplug the phone from your computer.

If your OpenVPN config is correct you can start OpenVPN Settings.

The current version apparently has a bug.

You have to select "OpenVPN", then select the OpenVPN configuration you want to load. 
You expect it to work but it won't, you have to deselect OpenVPN and select again, from there it will load the configuration, eventually prompting your for your password or whatever, and finally connect you.

Last thing I'd like to mention : **screenshots** on Android :-)
As root, you can take screenshots, look it up on the market, there are a dozen applications doing that. 

If you have any questions, concerns or comments, feel free to drop a line in the comments :-)






