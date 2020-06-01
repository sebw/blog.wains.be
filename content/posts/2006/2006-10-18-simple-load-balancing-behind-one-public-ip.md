---
date: 2006-10-18
title: "Simple Linux load balancing behind one public IP"
---







categories:
- Linux
- Security


Balance would help you setting up a SIMPLE load balanced cluster.
It will balance equally between nodes, which could still lead to troubles, if one machine is much slower/busier than the other.
It only manages 16 connections per nodes and 16 nodes.
If you want something more powerful, take a look at IPVSADM

[http://www.inlab.de/balance.html](http://www.inlab.de/balance.html)
[http://dag.wieers.com/packages/balance/](http://dag.wieers.com/packages/balance/)

Example :

**Machine A : Director / Load Balancer**

IP : 10.1.20.254

Start balance using this command : balance 80 10.1.20.1 10.1.20.2

Type : balance -i 80
then "show"

This will show you some stats

**Machine B : Node 1 / Web Server**

IP : 10.1.20.1

**Machine C : Node 2 / Web Server**

IP : 10.1.20.2

**Balance usage : **

balance [-b addr] [-B addr] [-t sec] [-T sec] [-adfpHM] 
          port [h1[:p1[:maxc1]] [!%] [ ... hN[:pN[:maxcN]]]]
  balance [-b addr] -i [-d] port
  balance [-b addr] -c cmd  [-d] port

  -a        enable channel autodisable option
  -b host   bind to specific address on listen
  -B host   bind to specific address for outgoing connections
  -c cmd    execute specified interactive command
  -d        debugging on
  -f        stay in foregound
  -i        interactive control
  -H        failover even if Hash Type is used
  -M        use MMAP instead of SHM for IPC
  -p        packetdump
  -t sec    specify connect timeout in seconds (default=5)
  -T sec    timeout (seconds) for select (0 => never) (default=0)
   !        separates channelgroups (declaring previous to be Round Robin)
   %        as !, but declaring previous group to be a Hash Type

More info : [http://www.linux.com/article.pl?sid=05/07/27/1729229](http://www.linux.com/article.pl?sid=05/07/27/1729229)
