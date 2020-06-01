---
date: 2005-06-27
title: "How to fix a broken GRUB"
---


**The story :**

For 3 days in a row, one of our Linux CentOS servers (small squid, DNS, MRTG, apache server running on a P3 450/192 Mb) has been randomly rebooting. After the usual checkup, I came to the conclusion it wasn't a power outtage nor a UPS issue and the box hadn't been hacked,.. The SMART table had critical values recorded, the hard drive was about to faint (a 1999 maxtor unit)

I only had a 4 Gb Western Digital drive available around to replace the faulty unit.

I used Norton Ghost 2003 to copy the system to the "new" drive. Ghost automatically detects the new unit is smaller than the old one and fixes the partition size issue in matter of seconds.. Once you've gone through these steps, the new unit is ready to reboot... well.. meant to reboot actually..

**The issue :**

When rebooting, the systems hangs on the word "GRUB" and then.. nothing.
This means GRUB is corrupted or broken, we just need to fix it.

**The fix :**

I fetched the server version ISO of CentOS 3.4 from which I could access the rescue mode that will help me fix GRUB

1. When you get to the CentOS installer prompt, type "**linux rescue**"...
2. Do not enable the network interfaces (not needed)
3. Once at the bash prompt type : **chroot /mnt/sysimage**
4. Once the system is mounted, type "**grub**"
5. Under grub, type "**root (hd0,0)**", it should mention what kind of partition it is (hd0,0 is what people would have in most case, depends on your partitioning scheme).
6. Type "**setup (hd0)**", grub will be reconfigured correctly

The output after the last command was :
`install /grub/stage1 (hd0) (hd0)1+15 p (hd0,0)/grub/stage2 /grub/grub.conf`

7. Quit grub, eject the CD and reboot the machine..

The system reboots !
