---
date: 2008-01-22
title: "Ubuntu 7.10 - screen doesnt lock when closing laptop lid"
---







categories:
- Linux


This used to work in Ubuntu 7.04..

Here's the fix until the feature works again (See bugreport [156226](https://bugs.launchpad.net/ubuntu/+source/gnome-power-manager/+bug/156226) in launchpad)

- Open gconf-editor

- Enable /apps/gnome-power-manager/lock/use_screensaver_settings

You should also enable "blank-screen" in the same section, I noticed the screen doesn't go blank when the lid is closed without it

Solution provided by [Pedro Villavicencio](https://bugs.launchpad.net/~pvillavi) in the comments of the bug report ([permalink](https://bugs.launchpad.net/ubuntu/+source/gnome-power-manager/+bug/156226/comments/5))

