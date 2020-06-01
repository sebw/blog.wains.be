---
date: 2017-11-17
title: "Create an IP alias on Mac OS"
---

Tested on High Sierra

Create an alias:

`sudo ifconfig en0 alias 192.168.0.101 255.255.255.0`

Remove the alias:

`sudo ifconfig en0 -alias 192.168.0.101`