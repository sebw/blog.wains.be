---
date: 2006-12-04
title: "Ubuntu / FC5 - low volume issue with Intel HDA onboard card"
---







categories:
- Linux


The bug is reported here :

[https://launchpad.net/distros/ubuntu/edgy/+source/alsa-driver/+bug/47755](https://launchpad.net/distros/ubuntu/edgy/+source/alsa-driver/+bug/47755)



My onboard card was recognized as "Generic 14f1 ID 5047".
`root@portable:~# head -5 /proc/asound/card0/codec*
Codec: Generic 14f1 ID 5047
Address: 0
Vendor Id: 0x14f15047
Subsystem Id: 0x1179ff31
Revision Id: 0x100000`

The volume was low and distorted at 100 % with alsa-drivers 1.0.11

I followed this page [http://www.alsa-project.org/alsa-doc/doc-php/template.php?company=Intel&card=ICH+southbridge+HD-audio+and+modem.&chip=ICH6%2C+ICH6M%2C+ICH7%2C+ESB2&module=hda-intel](http://www.alsa-project.org/alsa-doc/doc-php/template.php?company=Intel&card=ICH+southbridge+HD-audio+and+modem.&chip=ICH6%2C+ICH6M%2C+ICH7%2C+ESB2&module=hda-intel)
and upgraded manually to the alsa-drivers version 1.0.13

It fixed the issue.

The chip is now recognized as a "Conexant ID 5047"
`root@portable:/# head -5 /proc/asound/card0/codec*
Codec: Conexant ID 5047
Address: 0
Vendor Id: 0x14f15047
Subsystem Id: 0x1179ff31
Revision Id: 0x100000`
