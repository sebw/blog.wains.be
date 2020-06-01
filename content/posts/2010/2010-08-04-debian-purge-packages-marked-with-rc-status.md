---
date: 2010-08-04
title: "Debian - purge packages marked with rc status"
---







categories:
- Debian/Ubuntu


Packages with rc status are not completely removed from the system, configuration files are still present.

If you have a bunch of rc packages you need to purge, as root, type :

`dpkg --list |grep "^rc" | cut -d " " -f 3 | xargs sudo dpkg --purge`


Source : [http://joysofprogramming.com/remove-packages-marked-rc/](http://joysofprogramming.com/remove-packages-marked-rc/)
