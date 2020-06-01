---
date: 2007-07-12
title: "Squid cache manager error - socket- (13) Permission denied"
---







categories:
- Linux
- Proxy
- Security


If you get the error 

`socket: (13) Permission denied`

while trying to connect to the cache manager of Squid using cachemgr.cgi, it probably means SElinux is enabled and is preventing cgi files from making TCP connections.

**Quick and dirty fix : disabling SElinux**

Edit /etc/sysconfig/selinux

Change the value SELINUX to "disabled"

**Clean fix : make a rule in SElinux to allow the connection**

I don't know much about SElinux yet, so if someone feels like pointing me to the right direction or submitting something, it is welcomed :)
