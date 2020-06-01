---
date: 2007-12-09
title: "Debian Etch + DSPAM retraining with Web Interface"
---







categories:
- Debian/Ubuntu
- Linux
- Postfix


A while ago, I wrote [an howto](http://blog.wains.be/post/howto-postfix-chrooted-debian-etch-ssltls-smtp-auth-sasl-quota-vda-postfix-admin-with-virtual-usersdomains-autoreply-disclaimerautosignature-procmail-dspam-clamassassinclam/) explaining how to set up a Debian box with Postfix and DSPAM as anti-spam solution.

I haven't been able to test that howto in a real world environment yet (receiving and training the system with actual spam). Somehow, it has been reported by several persons that it was working (thanks guys !).

The howto was considering a small domain where the sysadmin would take care of retraining the system. (Don't get me started on that. I prefer retraining the system myself than having a miserable spam detection rate because "users" don't get involved in fighting spam.)

Somehow, the howto did not fit to customers needs, and so, I figured I should write an addendum to the 1st howto.

Basically, the customer doesn't always have a sysadmin available on site. The users must retrain DSPAM themselves.
DSPAM offers a web interface to manage emails, quarantines, etc.

The following howto tries to explain how to get the whole thing running, it involves several changes in DSPAM configuration.

Also, please note mod_auth_mysql is not available in the stable repo for Etch, the howto explains how to compile it.

**Here's the link to the howto :** [Debian + Postfix + DSPAM + WebUI + MySQL auth](http://blog.wains.be/pub/postfix/dspam-webui-debian.txt)

The howto is provided "as is". I could not find the time lately to thoroughly test it.
I decided to publish it since the documentation regarding the WebUI is pretty poor.

I repeat, the howto is still a work in progress ! To be continued.
Let me know if it works for you.
