---
date: 2006-12-31
title: "Ubuntu - Side buttons with Microsoft IntelliMouse Optical"
---







categories:
- Howto
- Linux


Tested working under Ubuntu 6.10
With this enabled, you can go backward and forward under Firefox (and probably other apps).

**sudo gedit /etc/X11/xorg.conf and add :**

`Option "ButtonMapping" "1 2 3 6 7 4 5"
Option "ZAxisMapping" "4 5"`



**sudo gedit /etc/X11/Xsession.d/63xmodmap (this is a new file) and add :**

`xmodmap -e "pointer = 1 2 3 4 5 6 7"
BINARY=$(which imwheel)
$BINARY -k -p -b "67"`


The line "Option "ZAxisMapping" "4 5"" was already present in xorg.conf
