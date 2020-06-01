---
date: 2009-01-16
title: "Unlocking the Belgacom Wireless/VoIP/ADSL Router aka Philips SNV6520/18"
---







categories:
- Hardware
- VoIP


![Belgacom Philips SNV6520/18](https://blog.wains.be/images/00-imageshack.jpg)

**THE BORING PART :**

For the impatients out there, just skip to "Mandatory warnings".

A while back, Belgacom, the major ISP in Belgium used to sell the router mentionned above along with their "triple-play" offer.

My dad just got a DSL connection and I got him that model on eBay. Not that I wanted that particular model or anything, I needed a wireless DSL router quick and for a reasonable price.

I figured I could set up VoIP so my dad could call me on my Asterisk SIP PBX at home using his brand new DSL connection, using an actual phone (the unit comes with two FXS ports which you can plug two analog phones in).

The thing is.. Belgacom locked the router with a proprietary firmware which restricts VoIP to their SIP servers only.
(if you specify sip.example.org as server, it will actually try to register on voip.belgacom.be)

This howto explains how to unlock the unit and not only get unlocked VoIP but also new features (like embedded DynDNS client) .

**MANDATORY WARNINGS :**

**You must understand that the following will void your warranty. 
If you install the unlocked firmware, you'll not be able to come back to Belgacom firmware. 
I don't have Belgacom TV, so I can't tell if the new firmware works with it.
If the upgrade fails for some reason, you'll probably end up with a paperweight, a quite original one I must say.
In case of power outage during the upgrade, the unit will probably get bricked.
DO NOT HOLD ME ACCOUNTABLE FOR ANY BRICKED ROUTER. I'M NOT PUTTING A GUN ON YOUR FOREHEAD TELLING YOU TO UPGRADE YOUR ROUTER. WHAT YOU ARE DOING, YOU'RE DOING IT AT YOUR OWN RISK. IF YOU ARE NOT COMFORTABLE WITH THE PROCESS, JUST DON'T DO IT.**

**THE HOWTO : **

**1. Download**

Grab the following file : [http://www.wains.be/pub/SNV6520unlocking.zip](http://www.wains.be/pub/SNV6520unlocking.zip)

`Important : The package above comes with firmware 2.09. 
Here's the link to the latest firmware (version 2.15) which fixes known wireless issues (which my dad had) : [http://www.wains.be/pub/SNV6520-FW2_15.zip](http://www.wains.be/pub/SNV6520-FW2_15.zip). 
I've upgraded from v2.09 to v2.15 without issue. 
It has been reported in the comments that jumping directly to 2.15 was causing problems. 
I recommend you upgrade to 2.09 and then to 2.15 to avoid troubles, so just proceed with 2.09 and then repeat the steps from step 5 for version 2.15.`

**2. Unzip the file**

Untip SNV6520unlocking.zip, it contains two files :
- the boot loader : iad-boot_v0.69d.0
- the firmware : FW_7908VoWBRA_v2.09.bin

Just make sure you write down all your settings before starting, as the unit will be reset to factory settings.

**3. Enable the "recovery" mode of your unit**

Read and make sure your understand before proceeding :

`1. Turn off the unit
2. Press the reset button located on the back while turning the unit on
3. Keep the button pressed for 2 seconds after power on
4. Release and then immediately press the reset button 3 times again (short press are fine)
5. WLAN and DSL LEDs should blink, this means the router is in recovery mode.`

**4. Upgrade the boot loader**

Make sure your computer has an IP in the 192.168.2.0/24 range.
Go to http://192.168.2.1
Upgrade target : select boot loader
Select iad-boot_v0.69d.0 and upgrade

When done, click on reboot then reset
It should take a minute or so.

**5. Upgrade the firmware**

When done, go back to http://192.168.2.1, you should see that you're now running v0.69 (upper right corner of the screen)

Upgrade target : select runtime code
Select FW_7908VoWBRA_v2.09.bin (or SMC7908VoWBRA-FW2_15.bin) and upgrade

When done, click on reboot then reset.

You should be done now, your unit should be running the SMC firmware after reboot.

**6. Enjoy**

Go to http://192.168.2.1
Password is "smcadmin"

You can now enjoy unlocked VoIP settings, dynamic DNS and probably other niceties.

**CONCLUSION : **

My dad now enjoys his internet connection wirelessly, and also calls me and belgian landlines for free through my home PBX :D

**In the comments, it is reported that Belgacom TV works after upgrade, but NOT AS NICELY AS BEFORE THE UPGRADE. You've been warned :-)**
