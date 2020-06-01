---
date: 2005-06-17
title: "Hacking the Orinoco Silver into an Orinoco Gold"
---

When inserting my Orinoco card into my laptop, dmesg was telling my card was able to do WEP 128 bits. After several failed attempts, I went with WEP 64 bits that worked fine.

dmesg returned this :

    eth1: Station identity 001f:0001:0008:0048
    eth1: Looks like a Lucent/Agere firmware version 4.02
    eth1: Ad-hoc demo mode supported
    eth1: IEEE standard IBSS ad-hoc mode supported
    eth1: WEP supported, 104-bit key
    eth1: MAC address 00:02:2D:XX:XX:XX
    eth1: Station name "HERMES I"
    eth1: ready
    eth1: index 0x01: Vcc 5.0, irq 3, io 0x0100-0x013f

Google led me to the article named "Hacking the Orinoco Silver into an Orinoco Gold" :
[http://www.inertramblings.com/archives/000741.html](http://www.inertramblings.com/archives/000741.html)

The site owner explains he's been able to turn his Orinoco Silver into a Gold very easily.

Here are the steps to follow, at your own risks of course, make sure the firmware update is available for your card:

1. download Alchemy 1.0.1 at [http://www.lincomatic.com/wireless/software.html](http://www.lincomatic.com/wireless/software.html)
2. edit the PDA file thanks to Alchemy, answers the questions. Accept the modifications made to the PDA file and refuse all modifications to serial number, MAC address, etc. You'll get a copy of your old PDA file and the new one (backup the old PDA file before doing anything to your card!!!)
3. flash your card firmware with the latest version to date (8.72 as of today). If you have an issue when updating the card, you can always get back to v4.00 (my version was 4.02)`

After these steps, I was able to connect my card with WEP 128 bits encryption, I now have an Orinoco Gold :) Not a single issue to report so far (as of dec 8, the card runs perfectly and the operation has been harmless), reception is perfect and stable..
