---
date: 2005-12-13
title: "SpamAssassin 3.1.0 crashed - tcp timeout issue"
---

SpamAssassin 3.1.0 crashed last week.
I looked up the logs and found out these errors

`Dec  5 00:15:04 fedora spamd[29470]: tcp timeout at
/usr/lib/perl5/site_perl/5.8.0/Mail/SpamAssassin/SpamdForkScaling.pm line 195.
Dec  5 00:15:04 fedora spamd[29470]: tcp timeout at
/usr/lib/perl5/site_perl/5.8.0/Mail/SpamAssassin/SpamdForkScaling.pm line 195.
Dec  5 00:28:22 fedora spamc[8763]: connect(AF_INET) to spamd at 127.0.0.1
failed, retrying (#1 of 3): Connection refused
Dec  5 00:28:23 fedora spamc[8763]: connect(AF_INET) to spamd at 127.0.0.1
failed, retrying (#2 of 3): Connection refused
Dec  5 00:28:24 fedora spamc[8763]: connect(AF_INET) to spamd at 127.0.0.1
failed, retrying (#3 of 3): Connection refused`

A bug report was already filled into apache bugzilla system.
It's available here : [http://issues.apache.org/SpamAssassin/show_bug.cgi?id=4696](http://issues.apache.org/SpamAssassin/show_bug.cgi?id=4696)
