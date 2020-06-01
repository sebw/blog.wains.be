---
date: 2006-10-19
title: "Tool of the day - iptstate"
---







categories:
- Linux
- Security
- Tools


**iptstate provides live iptables monitoring**

iptstates shows all iptables activity in a top-style display:

  IPTables - State Top
Version: 1.3          Sort: SrcIP           s to change sorting
Source                Destination       Proto   State        TTL
192.168.1.1,3834     193.40.133.134,123  udp     0:00:28
192.168.1.1,3822     216.58.31.84,123    udp     0:02:53
192.168.1.1,3828     216.52.237.153,123  udp     0:02:47
192.168.1.10,43496   192.168.1.26,22     tcp     ESTABLISHED  119:59:59
192.168.1.11,57252   71.87.212.168,8080  tcp     ESTABLISHED   29:43:53
192.168.1.25,57505   209.40.99.8,80      tcp     ESTABLISHED    3:48:32

iptstate has a number of useful commands, such as setting the refresh interval, sorting by different columns values, resolving domain names, and a number of interactive commands to use while it's running. Read the fine man page to learn them all. 

[http://www.phildev.net/iptstate/](http://www.phildev.net/iptstate/)
