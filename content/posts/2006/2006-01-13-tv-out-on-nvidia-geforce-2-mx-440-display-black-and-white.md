---
date: 2006-01-13
title: "TV out on Nvidia displaying in black and white with scart & S-VHS"
---

I've been scratching my head for a while on this issue but I finally found a fix !
I tried about anything, knowing it used to work a while ago (like 2 years from now with older drivers).



My Nvidia Geforce 2 MX 440 was displaying a black and white output when using a S-VHS (or S-video) cable plugged into my scart TV 

**My drivers :**
ForceWare Release 80
Version: 81.98
Release Date: December 21, 2005
WHQL Certified

OS : Windows XP SP2

![Peritel](https://blog.wains.be/images/peritel.jpg)
_The scart plug_

![S-VHS cable](https://blog.wains.be/images/svhs.jpg)
_Connectors from the S-VHS cable_

The $10 fix : get a coax cable, it'll work on coax...

The $0 fix :
- Set the video card to display using "clone mode"
- Set the TV as the main display

Then right click the TV icon and go to advanced settings for the TV format :
![](https://blog.wains.be/images/nvidia1.gif)

Set the output as composite even though you actually use S-VHS :
![](https://blog.wains.be/images/nvidia2.gif)

It should work now !

`Edit Feb. 6, 2006 : the $0 trick no longer work with my new GeForce 6200 due to some different drivers implementation :( 
The $1 trick available at [http://camp0s.altervista.org/sVideo/sVideo.htm](http://camp0s.altervista.org/sVideo/sVideo.htm) worked great for me ! :D :D :D
Give it a go if the software/drivers trick does not work for you
`

Edit Sep. 8, 2007 : new link for the $1 trick http://www.camp0s.com/pc_related/svideo/svideo.php


