---
date: 2007-04-03
title: "Set your keyboard rate and repeat delay under Linux"
---







categories:
- Linux


Just found this out, didn't know that command existed.

`$ kbdrate -r30 -d0`

This sets the repeat rate to 30 characters per second (which is the maximum value) and a repeat delay of 250 ms (which is the lowest possible).

You may sometimes notice your keyboard rate/repeat delay is slow after attaching it to a running system which was booted without a keyboard attached. I guess this command would help. No guarantee on that though.
