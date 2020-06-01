---
date: 2006-11-18
title: "Postfix chroot + SASL authentication (saslauthd)"
---







categories:
- Howto
- Linux
- Postfix
- Security


**Scenario : **

You run Postfix non-chrooted with SMTP auth though SASL authentication (using saslauthd)

**The issue :**

When chrooted, Postfix needs access to saslauthd sock file, which is not in the chroot area

**The fix :**



We consider Postfix chroot is under /var/spool/postfix

`mkdir -p /var/spool/postfix/var/run
mv /var/run/saslauthd /var/spool/postfix/var/run/
ln -s /var/spool/postfix/var/run/saslauthd /var/run`

Configure Postfix to run chrooted (script available here : [http://blog.wains.be/pub/postfix-chroot](http://blog.wains.be/pub/postfix-chroot)


