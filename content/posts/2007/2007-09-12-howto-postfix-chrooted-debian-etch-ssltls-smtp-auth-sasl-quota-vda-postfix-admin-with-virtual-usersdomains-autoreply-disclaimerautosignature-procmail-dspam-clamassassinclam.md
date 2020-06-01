---
date: 2007-09-12
title: "Howto - Postfix chrooted + Debian Etch + SSL/TLS + SMTP AUTH SASL + Quota"
---






  (VDA) + Postfix Admin with Virtual Users/Domains + Autoreply + Disclaimer/Autosignature
  + Procmail + DSPAM + Clamassassin/ClamAV + RBL checks + Mime checks + Dovecot IMAP(S)/POP3(S)
  chrooted'

categories:
- Howto
- Linux
- Postfix


I have tried to document the new email setup I'm willing to install at work.

My current setup is running under CentOS, uses SpamAssassin and courier-imap.
I'm trying to make the switch to different technologies : Debian, DSPAM and Dovecot.

DSPAM and Dovecot are apparently known to use less ressources, and ressources are an issue where I work (a non-profit org)

To avoid any problem of crazy text reformatting by Wordpress, the howto is downloadable as a text file :

Download it here :
[http://blog.wains.be/pub/postfix/postfix-debian.txt](http://blog.wains.be/pub/postfix/postfix-debian.txt)

Please, if you use it, let me know and tell me how it went.
There must be some mistakes around and I'd be grateful if you can provide some feedback on the quality of this documentation.

**Please note : I have NOT tested Dspam in a live environment yet, so I can't swear it would be efficient in this setup.**

**USE AT YOUR OWN RISK. YOU MUST TEST YOUR SETUP BEFORE PUTTING IN PRODUCTION.**
