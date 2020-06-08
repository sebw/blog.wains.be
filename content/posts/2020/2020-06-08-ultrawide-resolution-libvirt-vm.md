---
date: 2020-06-08
title: Ultra wide 3440x1440 resolution on a libvirt VM
---

I've been trying to add the new mode for my ultra wide monitor with resolution 3440x1440.

I was getting this error:

```
X Error of failed request:  BadName (named color or font does not exist)
```

Before my wide screen, I used to configure my VM with QXL drivers.

It turns out QXL won't accept the ultrawide resolution.

## Solution

1. reconfigure your VM with Virtio graphical drivers

2. execute this script:

```bash
#!/bin/bash
DISP=Virtual-1
cvt 3440 1440
# use the values returned by cvt
xrandr --newmode $DISP "3440x1440"  419.50  3440 3696 4064 4688  1440 1443 1453 1493 -hsync +vsync
xrandr --addmode  3440x1440
xrandr --output $DISP --mode 3440x1440
xrandr -s 3440x1440
```