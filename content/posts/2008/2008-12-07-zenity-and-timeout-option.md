---
date: 2008-12-07
title: "Zenity and timeout option"
---







categories:
- Bash
- Linux
- Tools


Lately I installed [rsnapshot](http://www.rsnapshot.org) on my lappy to get my files backed up on my home server during the night, in case the laptop is on.

Rsnapshot is only CLI, I still somewhat wanted to get some kind of visual notification saying the backup was starting (and ending).. just to avoid shutting down my machine while the backup process was still on, for example.

Rsnapshot allows you, throught the cmd_postexec and cmd_preexec options, to run scripts before and after the backup job.

I came up with scripts using [Zenity](http://live.gnome.org/Zenity) for visual notifications.

The thing is, the cmd_postexec script must complete before the backup can begin, and the only documented way I found to start the backup was to click OK on the zenity notification. This would be annoying if I'm not at the computer at that time (which is very likely :) ). The notification popup would just wait for my input to start the backup job.. not good.

I tried to figure out a decent way to get rid of the popup after a few second, but I didn't have to search for too long.. Indeed, [Marco spotted that Zenity has an undocumented timeout option](http://marcotmarcot.blogspot.com/2008/06/undocumented-timeout-option-in-zenity.html) doing exactly what I wanted.

Odd that it is not documented..

Thanks to Marco !

For the curious, here's the cmd_postexec script (rsnapshot running as root and opening the popup on the user's desktop) :

`#!/bin/bash
su USERNAME -c 'export DISPLAY=:0.0; zenity --timeout 30 --info --text "Rsnapshot backup will start in 30 seconds"' 
exit 0`

