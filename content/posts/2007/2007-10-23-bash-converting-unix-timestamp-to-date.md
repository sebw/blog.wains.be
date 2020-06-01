---
date: 2007-10-23
title: "Bash - converting unix timestamp to date "
---







categories:
- Bash
- Linux


Under Debian or RHEL4 and later, it can be as simple as :

`$ date -d @1193144433
Tue Oct 23 15:00:33 CEST 2007`

But that command doesn't work under Red Hat EL 3, so you should use the following :

`$ date --date "1970-01-01 1193144433 sec" "+%Y-%m-%d %T"
2007-10-23 15:00:33`

The output is different though
You can always use the previous command under Ubuntu/RHEL4+ or the simpler :

`$ date -d @1193144433 "+%Y-%m-%d %T"
2007-10-23 15:00:33`

Converting a date to unix timestamp :

`$ date -d "2007-10-23 15:00:23" "+%s"
1193144423`


Add `"--utc"` to the commands if you want to get the UTC time.
