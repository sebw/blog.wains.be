---
date: 2016-03-21
title: "Fixing X11 connection rejected because of wrong authentication"
---

You get either "X11 connection uses different authentication protocol" or "X11 connection rejected because of wrong authentication".

The thing is, this is a very generic error message that will take you to multiple very different fixes.

Many telling you to set a variable here and change a setting there.

In my case, it was because the SSH server had a configuration under ```/etc/ssh/sshrc```.

I found the answer [here](https://serverfault.com/questions/278743/ssh-x11-not-working/378412#378412).

In my case, sshrc only had comments as I had stopped to use the file, so I just removed the config file and ```ssh -X user@server xclock``` worked again.

