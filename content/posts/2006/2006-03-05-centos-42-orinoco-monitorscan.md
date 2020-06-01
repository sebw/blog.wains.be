---
date: 2006-03-05
title: "CentOS 4.2 + Orinoco Monitor/Scan"
---

According to [http://blog.wains.be/?p=25](http://blog.wains.be/?p=25), it was quite a pain to get the orinoco drivers with scan and monitor capabilities..

Being sick at the debianess of Ubuntu, I switched my laptop to the good old and lovely CentOS 4.2.

Having the orinoco_cs drivers with monitor mode running was a question of minutes..

Grab the drivers at the usual place : [http://www.projectiwear.org/~plasmahh/orinoco.html](http://www.projectiwear.org/~plasmahh/orinoco.html)
I grabbed this one : http://www.projectiwear.org/~plasmahh/orinoco-0.13e-SN-7.tar.bz2

Untar somewhere on the disk.. make a backup of the good orinoco drivers located at /lib/modules/2.6.9-22.0.2.EL/kernel/drivers/net/wireless

Just type make.. it should compile without any glitches, eject your wireless card, copy the new drivers over the old one, insert your orinoco card and you should be done..

iwpriv eth1 should return this :
[root@pc2](984)# iwpriv eth1
eth1      Available private ioctl :
          force_reset      (8BE0) : set   0       & get   0
          card_reset       (8BE1) : set   0       & get   0
          set_port3        (8BE2) : set   1 int   & get   0
          get_port3        (8BE3) : set   0       & get   1 int
          set_preamble     (8BE4) : set   1 int   & get   0
          get_preamble     (8BE5) : set   0       & get   1 int
          set_ibssport     (8BE6) : set   1 int   & get   0
          get_ibssport     (8BE7) : set   0       & get   1 int
          monitor          (8BE8) : set   2 int   & get   0
          dump_recs        (8BFF) : set   0       & get   0

[root@pc2](986)# iwlist eth1 scan
eth1      Scan completed :
          Cell 01 - Address: 00:0F:66:xx:xx:xx
                    ESSID:"xxx"
                    Mode:Managed
                    Frequency:2.412GHz (Channel 1)
                    Quality:128/92  Signal level=-64 dBm  Noise level=-95 dBm
                    Encryption key:on

Kismet should enable monitor mode by itself, otherwise :

root@home:~ # iwpriv eth0 monitor m c
    m - one of the following
	0 - disable monitor mode
	1 - enable monitor mode with Prism2 header info prepended
	    to packet (ARPHRD_IEEE80211_PRISM)
	2 - enable monitor mode with no Prism2 info (ARPHRD_IEEE80211)
    c - channel to monitor

