---
date: 2007-07-12
title: "Gnome - change autohide panel behavior"
---







categories:
- Gnome
- Linux


Tested under Ubuntu only

You can change some settings of the autohide feature of gnome panels (task bar).

Like by default, if set to autohide, the bar is not disappearing completely.

**In a terminal :**
`$ gconf-editor`

Then go under the key :
- /apps/panel/toplevels/top_panel_screen0
- /apps/panel/toplevels/bottom_panel_screen0

**The interesting keys are :**
auto_hide_size : set this to 0 if you want the bar to completely disappear
hide_delay : change how fast the bar would hide
unhide_delay : same for unhide..


