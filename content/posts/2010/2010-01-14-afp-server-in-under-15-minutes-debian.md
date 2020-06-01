---
date: 2010-01-14
title: "AFP server in under 15 minutes (Debian)"
---







categories:
- Apple/Mac OS
- Debian/Ubuntu
- Howto


_Tested under Debian Lenny 32 bits._

This howto is based on [http://www.kremalicious.com/2008/06/ubuntu-as-mac-file-server-and-time-machine-volume/](http://www.kremalicious.com/2008/06/ubuntu-as-mac-file-server-and-time-machine-volume/)
Matthias' post is very comprehensive. This post is basically a raw copy paste of commands, if you want more information, go see Matthias post.
If you find this useful, please give credit to Matthias :-)


**Why AFP ([Apple Filing Protocol](http://en.wikipedia.org/wiki/Apple_Filing_Protocol)) ?**

I wanted to see if AFP was faster than SMB.
A quick test showed my Macbook (running OS 10.6.2) is transfering files 20 % faster on AFP than SMB.


**Build netatalk to support encryption **

Starting with Netatalk version 2.0.4 (and Debian Squeeze) you won't need to rebuild to support SSL (see Frank's comment).

Lenny comes with version 2.0.3 so we still need to go through recompilation (which I recommend doing on another box).

`# apt-get build-dep netatalk


**Install modified version of netatalk**

`# dpkg -i ../netatalk_2*.deb


**/etc/default/netatalk**

`ATALKD_RUN=no
PAPD_RUN=no
CNID_METAD_RUN=yes
AFPD_RUN=yes
TIMELORD_RUN=no
A2BOOT_RUN=no`


**/etc/netatalk/afpd.conf**

`- -transall -uamlist uams_dhx.so -nosavepassword`


**/etc/netatalk/AppleVolumes.default**

`/home/seb "Seb's share" allow:seb`


**Start netatalk**

`/etc/init.d/netatalk start`


**Advertise the service with Avahi**

You can skip this step, but it'd mean the server doesn't magically appear as a Shared drive in your Finder.

`apt-get install avahi-daemon`


**/etc/avahi/services/afpd.service**


`


%h AFP

_afpovertcp._tcp
548


_device-info._tcp
0
model=Xserve

`


`# /etc/init.d/avahi-daemon restart`


**Firewall :**

Allow tcp/548 (netatalk) and tcp/5353 (avahi)
