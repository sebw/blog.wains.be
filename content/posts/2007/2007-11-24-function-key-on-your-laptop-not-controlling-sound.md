---
date: 2007-11-24
title: "Function key on your laptop not controlling sound ?"
---







categories:
- Hardware
- Linux


OK, this one is dumb.

Until a few months ago, I was able to control the sound volume on my laptop using the dedicated function key (controlling the master output).

Then, all messed up.

Along with the dead function key, the master volume no longer had any effect on volume. 
Volume could only be controlled by the PCM switch. And the function key was controlling the master output !

Today again, I tried to manually install the latest alsa-drivers to see if it was fixing the master volume issue. Was a no go.

Finally tonight, Google came up with an answer !

[https://help.ubuntu.com/community/MacBook#head-d374bb9e1b7183c133759a8c6877a34c50c4ba7d](https://help.ubuntu.com/community/MacBook#head-d374bb9e1b7183c133759a8c6877a34c50c4ba7d)

Gnome actually has a setting allowing you to choose which input the function key should control. Gosh.

**Solution :**

- Go to preferences menu
- Devices tab
- Default mixer tracks
- Select the track you want to control (PCM for me)

This doesn't solve the master volume issue but I couldn't care less now.
