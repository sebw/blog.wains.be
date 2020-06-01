---
date: 2007-07-28
title: "Howto - fix video playback issues when using Beryl/Compiz/Compiz Fusion"
---







categories:
- Linux


I have used Beryl, Fusion and now run Compiz Fusion.

I have been having the following issue since I first tried Beryl but did not care much about it until today.

When I was playing a video (totem, VLC, etc..), I was getting a black screen instead of the video. The video would show up if I was moving the window around, but it would randomly become black again after a while or if I was doing anything while the video was playing.

My graphic card is an Intel, a friend reported no issue with an nVidia card :
`VGA compatible controller: Intel Corporation Mobile 945GM/GMS/940GML Express Integrated Graphics Controller (rev 03)`

**The fix :**

Open gstreamer-properties from the command line :
`$ gstreamer-properties`

Go under the video tab and change the default output plugin from "autodetect" to "X Window System (no xv)"

Voilà !
