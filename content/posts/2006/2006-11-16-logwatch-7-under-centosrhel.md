---
date: 2006-11-16
title: "Logwatch 7 under CentOS/RHEL"
---







categories:
- Linux
- Security


I had the regular logwatch 5.2.2 running on my CentOS 3 box.
For some legal reason, I keep 52 weeks worth of logs.

Logwatch started behaving weird lately.. parsing events being 1 year old, it was a random mess of yesterday's events and last year logs.

I upgraded to Logwatch 7 (available at [http://repo.securityteam.us/repository/](http://repo.securityteam.us/repository/))

It looks like it fixed the issue.
