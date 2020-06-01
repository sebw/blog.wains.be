---
date: 2007-08-20
title: "Record entries in log files with logger"
---







categories:
- Linux


Call me Mr. Obvious on this one, but I actually always need to check out the manpage for this...

If you want to record entries in logs file :

Example here in daemon.log

`$ sudo logger -p daemon.warn
"This is a test"`

Where daemon is the facility, warn is the level

Hit Ctrl+C when done.

Result in /var/log/daemon.log :
`Aug 20 13:07:16 hostname user: "This is a test"`


Check /etc/syslog.conf to know the facilities :

auth,authpriv.*                 /var/log/auth.log
*.*;auth,authpriv.none          -/var/log/syslog
#cron.*                         /var/log/cron.log
daemon.*                        -/var/log/daemon.log
kern.*                          -/var/log/kern.log
lpr.*                           -/var/log/lpr.log
mail.*                          -/var/log/mail.log
user.*                          -/var/log/user.log


This morning I was asked on launchpad to send the content of daemon.log after a reboot..
I used logger to mark when I was about to reboot, and when the machine was back up.
It allowed me to cut the interesting snippet out of the whole log easily.
