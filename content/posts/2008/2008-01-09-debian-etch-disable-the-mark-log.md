---
date: 2008-01-09
title: "Debian Etch - disable the -- MARK -- log"
---







categories:
- Debian/Ubuntu
- Linux
- NSLU2


I installed [Debian on an NSLU2](http://www.cyrius.com/debian/nslu2/).

The system is running off a USB thumb drive.

In order to maximize the lifetime of the drive, I need to limit the number of writes to it.

-- MARK -- entries in the logs are (from my understanding) pretty useless for that system, here's how to disable them.

**Edit /etc/defaults/syslogd**

Change the SYSLOGD option to the following :

`SYSLOGD="-m 0"`


Restart syslogd :
`invoke-rc.d sysklogd restart`
