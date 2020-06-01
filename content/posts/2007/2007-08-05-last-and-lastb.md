---
date: 2007-08-05
title: "last and lastb"
---







categories:
- Linux


From the man pages :

**Last** searches back through the file /var/log/wtmp (or the file designated by the -f flag) and **displays a list of all users logged in (and out)** since that file was created.  Names of users and tty’s can be given, in which case  last  will  show  only  those  entries matching the arguments. Names of ttys can be abbreviated, thus last 0 is the same as last tty0.

**Lastb** is the same as last, except that by default it shows a log of the file  /var/log/btmp, which contains all **the bad login attempts**.

Knowing when the machine was rebooted :
`$ last reboot
reboot   system boot  2.6.20-16-generi Sun Aug  5 18:17 - 20:45  (02:27)    
reboot   system boot  2.6.20-16-generi Sun Aug  5 16:32 - 17:37  (01:04)    
reboot   system boot  2.6.20-16-generi Sun Aug  5 14:13 - 15:56  (01:42)    
reboot   system boot  2.6.20-16-generi Sun Aug  5 12:18 - 13:42  (01:24)    
reboot   system boot  2.6.20-16-generi Sun Aug  5 09:48 - 11:27  (01:39)    
reboot   system boot  2.6.20-16-generi Sat Aug  4 21:01 - 00:01  (03:00)    
reboot   system boot  2.6.20-16-generi Fri Aug  3 20:16 - 00:38  (04:22)    
reboot   system boot  2.6.20-16-generi Fri Aug  3 19:35 - 20:15  (00:39)    
reboot   system boot  2.6.20-16-generi Thu Aug  2 23:01 - 00:28  (01:26)    
reboot   system boot  2.6.20-16-generi Thu Aug  2 18:17 - 23:00  (04:42)    
reboot   system boot  2.6.20-16-generi Thu Aug  2 12:30 - 12:53  (00:23)    
reboot   system boot  2.6.20-16-generi Wed Aug  1 22:00 - 23:55  (01:54)    `

