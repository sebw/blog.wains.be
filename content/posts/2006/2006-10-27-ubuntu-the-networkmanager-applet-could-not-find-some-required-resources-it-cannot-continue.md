---
date: 2006-10-27
title: "Ubuntu - The NetworkManager applet could not find some required resources."
---






  It cannot continue.'

categories:
- Howto
- Linux


The NetworkManager applet could not find some required resources. It cannot continue. 

To fix the issue, you just need to type the following line at the terminal and reboot :

`sudo gtk-update-icon-cache -f /usr/share/icons/hicolor`

What is gtk-update-icon-cache :

`DESCRIPTION
       gtk-update-icon-cache creates mmap()able cache files for icon themes.
.
       It expects to be given the path to a icon theme directory containing an
       index.theme, e.g.  /usr/share/icons/hicolor, and writes a
       icon-theme.cache containing cached information about the icons in the
       directory tree below the given directory.
.
       GTK+ can use the cache files created by gtk-update-icon-cache to avoid
       a lot of system call and disk seek overhead when the application
       starts. Since the format of the cache files allows them to be mmap()ed
       shared between multiple applications, the overall memory consumption is
       reduced as well.`

This is a bug in NetworkManager : https://launchpad.net/ubuntu/+source/network-manager/+bug/37128

A fix has been released.


