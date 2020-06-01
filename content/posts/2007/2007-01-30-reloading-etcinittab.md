---
date: 2007-01-30
title: "Reloading /etc/inittab"
---







categories:
- Linux


After any change made to /etc/inittab (like removing useless mingetty's), type this to restart the init process without rebooting :

`init q`
