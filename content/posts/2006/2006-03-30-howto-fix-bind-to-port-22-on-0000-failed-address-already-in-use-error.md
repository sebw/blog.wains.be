---
date: 2006-03-30
title: "Fix the Bind to port 22 on 0.0.0.0 failed - Address already in use error"
---

**The error when starting and restarting sshd :**
`Mar 30 23:35:11 x sshd[9151]: Server listening on :: port 22.
Mar 30 23:35:11 x sshd[9151]: error: Bind to port 22 on 0.0.0.0 failed: Address already in use.
Mar 30 23:38:07 x sshd[9151]: Received signal 15; terminating.
Mar 30 23:38:07 x sshd[1977]: Server listening on :: port 22.
Mar 30 23:38:07 x sshd[1977]: error: Bind to port 22 on 0.0.0.0 failed: Address already in use.`

**A successful connection made to the server despite the issue :**
`Mar 30 23:40:46 x sshd[2421]: Accepted publickey for root from ::ffff:xxx.xxx.xxx.xxx port 13586 ssh2`

**The fix : disable IPv6 : **
`echo "alias net-pf-10 off" >> /etc/modprobe.conf
restart the machine`

**Restarting the machine with IPv6 disabled :**
`Mar 30 23:47:57 x sshd[1957]: Server listening on 0.0.0.0 port 22.
Mar 30 23:48:46 x sshd[2470]: Accepted publickey for root from xxx.xxx.xxx.xxx port 13600 ssh2`
