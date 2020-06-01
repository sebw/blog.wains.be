---
date: 2006-10-09
title: "tcp_wrappers ACL for your SSH server"
---







categories:
- Howto
- Linux
- Security
- SSH


Among the many protections you can set to restrict connections to your server, there's tcp_wrappers that turns out to be pretty useful.


**Edit /etc/hosts.sshd**
Put into this file all the IP's, hostnames (avoid this as much as possible) or ranges allowed to ssh into the machine

E.g :
`192.168.1.1
10.0.0.
66.77.
*.somedomain.be`



**Edit /etc/hosts.allow**

Add this line at the beginning : 
sshd: /etc/hosts.sshd

Add this line at the end :
ALL : ALL : spawn (/usr/sbin/safe_finger -l @%h | /bin/mail -s "Port Denial - daemon %d hostname %h IP %a" root;  /bin/echo %a >> /var/log/port.denial.log) & : DENY


**Edit /etc/hosts.deny**

`sshd: ALL
ALL : /var/log/port.denial.log`


At any SSH attempt from an unauthorized person, the IP will be logged into /var/log/port.denial.log, meaning a ban to life unless you remove it from the log file.

You'd get a warning email along about the failed attempt
