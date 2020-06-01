---
date: 2007-12-28
title: "CentOS 5 - sending logs to a central log server"
---







categories:
- Linux
- Red Hat/CentOS
- Security


**On the log server :**

Edit /etc/sysconfig/syslog and change SYSLOGD_OPTIONS to match the following :
`SYSLOGD_OPTIONS="-m 0 -r -s example.com"`

-r : listen over the network, only necessary for log servers
-s : strip that value out of the logs (client.example.com would become client in the logs)

Restart the service :
`# service syslog restart`

The server will start listening on UDP/514

Make sure you allow that port in the firewall configuration on the log server



**On the "client" (machine sending the logs) :**

Edit /etc/syslog.conf and add the following line :
`*.*	@loghost.example.com`

By adding that line and keeping the default config, the logs will be stored on the client machine and sent to the log server as well.

Restart the service :
`# service syslog restart`

Restart a service (like ntpd.. whatever) and check the log messages appearing on the log server.


Obviously, this post is just a reminder.
Please remember syslog uses UDP, which is an unreliable transport.
During an attack, packets could be dropped and log messages along.
Also there's no authentication, an attacker could send fake log messages to the log server.
Logs are sent in clear text as well.

Syslog-ng addresses all these issues :
[http://www.balabit.com/network-security/syslog-ng/](http://www.balabit.com/network-security/syslog-ng/)
