---
date: 2006-12-05
title: "Problems with accents in Samba"
---







categories:
- Linux


Well I had problems with accents in filenames...

I tried changing the filesystem localization to french, it didn't help
I tried changing the system locale (/etc/sysconfig/i18n under CentOS/RHEL), it didn't help 

I figured out the accents trouble was only occuring when dropping a file though smb

I then finally found out these parameters to add in your smb.conf configuration file :

`dos charset = CP850
unix charset = ISO8859-1
display charset = LOCALE
`

It fixed the issue for me with the FS and system locale always under en_US
