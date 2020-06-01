---
date: 2007-07-12
title: "Faster Gnome menus"
---







categories:
- Gnome
- Linux


Edit the file ".gtkrc-2.0" under your home directory :
$ cd ~
$ gedit ./gtkrc-2.0

Add :
`gtk-menu-popup-delay = 100`

100 for 100 ms

Log off and log back in.
