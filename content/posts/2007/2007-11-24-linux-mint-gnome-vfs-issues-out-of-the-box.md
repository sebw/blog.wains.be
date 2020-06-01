---
date: 2007-11-24
title: "Linux Mint - gnome-vfs issues out of the box"
---







categories:
- Linux


Yesterday I switched from Ubuntu to Linux Mint. I won't discuss here the pros and cons of Mint since I first tested it yesterday and decided to install it on my laptop on the same day.

Everything is supposed to work out of the box under Mint.
Except it is not true. Apparently, Mint doesn't ship a bunch of packages that come by default under Ubuntu (particularly Evolution, tsclient, etc.).

As a result I was not able to open certain files stored on my Synology server directly within Nautilus (through SSH).
Most particularly OpenOffice docs and music files.

For docs, OpenOffice would fire up (splashscreen appears), then nothing.. No error message, nothing.

For music files, Totem would complain about a missing plugin.

Both issues are related to gnome-vfs support which is not installed for the program you are trying to run.

**Solution :**

Totem : install totem-gstreamer and delete totem-xine
OpenOffice : install openoffice.org-gnome
