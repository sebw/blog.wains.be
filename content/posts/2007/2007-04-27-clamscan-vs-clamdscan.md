---
date: 2007-04-27
title: "clamscan vs. clamdscan"
---







categories:
- Linux


If you're wondering what is best...

![Image Hosted by ImageShack.us](https://blog.wains.be/images/00-imageshack.jpg)

![Image Hosted by ImageShack.us](https://blog.wains.be/images/00-imageshack.jpg)

I switched back to clamdscan at 5pm.
Actually my system was using clamdscan under amavisd-new.
After switching to clamassassin, it was using clamscan by default, I noticed that after a while, seeing how long it was taking for emails to be processed.

Use ./configure --enable-clamdscan when building clamassassin (see [README](http://jameslick.com/clamassassin/clamassassin-1.2.4/README))
