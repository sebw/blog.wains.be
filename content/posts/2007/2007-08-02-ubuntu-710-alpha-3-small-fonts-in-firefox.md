---
date: 2007-08-02
title: "Ubuntu 7.10 Alpha 3 - small fonts in Firefox"
---







categories:
- Firefox
- Linux


Fonts are smaller in Firefox than under Gnome.

If you want to fix that small (and hopefully temporary) issue, under Firefox type in the URL bar :

"about:config"

Then in the filter box, search for "dpi"

This will filter out the value "layout.css.dpi" having a default value of "-1"

Change that value to "0" and restart Firefox, the fonts will be the same size as under Gnome.

Edit : Apparently, it is fixed in apha 4.

Edit 2 : issue back in alpha 5 (upgraded from alpha 4)

Edit 3 : Tim is reporting the issue is not present on a fresh alpha 5 install...
