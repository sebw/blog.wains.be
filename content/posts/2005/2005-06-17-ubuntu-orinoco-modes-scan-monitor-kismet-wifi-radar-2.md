---
date: 2005-06-17
title: "Ubuntu + Orinoco + Modes Scan & Monitor + Kismet + Wifi-Radar"
---

Since writing this article, I switched my laptop to CentOS 4
My Orinoco experiences under CentOS are available at : [http://blog.wains.be/?p=68](http://blog.wains.be/?p=68)

**MONITOR MODE ONLY**

I've been using the default ubuntu drivers for my orinoco card for a while.
I used a patch available on kismet website to get monitor capabilities.

[http://www.kismetwireless.net/code/orinoco-2.6.9-rfmon-dragorn-1.diff](http://www.kismetwireless.net/code/orinoco-2.6.9-rfmon-dragorn-1.diff)

I followed this guide to get the modules compiled :

[https://wiki.ubuntu.com//OrinocoMonitorKismet2005Hoary
](https://wiki.ubuntu.com//OrinocoMonitorKismet2005Hoary)

Kismet 2004 was working perfectly and was stable. I'd avoid Kismet 2005 for some reason, it was very unstable under Hoary. I use the 2004 version from Ubuntu backports :

To access Ubuntu backports, add this to your /etc/apt/sources.list, the steps are available on ubuntuguide.org

    deb http://ubuntu-backports.mirrormax.net/ hoary-backports main universe multiverse restricted
    deb http://ubuntu-backports.mirrormax.net/ hoary-extras main universe multiverse restricted

**MONITOR & SCAN MODES**

Having found out wifi-radar recently (wifi connection manager), I needed the scan mode of my card.

By default, the command "iwlist eth1 scan" was saying scanning was not supported by the card.
Now, the command returns this, proof that scanning works (except there's no network around at that time) :

    root@portable:/ # iwlist eth1 scan
    eth1 No scan results

In the first place, I found out some drivers that were giving me scan capabilities but monitor mode was really unstable. It was the official 0.15 drivers from Orinoco.

After more researches, I found out THE drivers that were giving perfectly stable scan & monitor modes.

[http://www.tzi.de/~plasmahh/orinoco.html](http://www.tzi.de/~plasmahh/orinoco.html)

It's based on version 0.13 from Orinoco that is the last stable drivers according to plasmahh. I use v0.13e Rev. 7 that works perfectly. I followed the step from ubuntulinux.org wiki (see link above) to compile the drivers.
Take care of its Makefile though, it specifies /lib as the source directory instead of the usual /usr/src/linux

This is my custom config :

    ORINOCODIR = $(shell pwd)
    #KERNEL_VERSION = $(shell uname -r)
    KERNEL_VERSION = 2.6.10-5-686
    KERNEL_SRC = /usr/src/linux

To make sure monitor works, use iwpriv :

    root@portable:/ # iwpriv eth1
    eth1 Available private ioctl :
    force_reset (8BE0) : set 0 & get 0
    card_reset (8BE1) : set 0 & get 0
    set_port3 (8BE2) : set 1 int & get 0
    get_port3 (8BE3) : set 0 & get 1 int
    set_preamble (8BE4) : set 1 int & get 0
    get_preamble (8BE5) : set 0 & get 1 int
    set_ibssport (8BE6) : set 1 int & get 0
    get_ibssport (8BE7) : set 0 & get 1 int
    monitor (8BE8) : set 2 int & get 0
    dump_recs (8BFF) : set 0 & get 0

If you are looking for something similar to Wifi-radar, check out GtkWifi at [http://sourceforge.net/projects/gtkwifi/](http://sourceforge.net/projects/gtkwifi/)
