---
date: 2007-03-24
title: "Unsolvable - Gratuitous ARP from our Windows 2000 server with Intel Pro 1000"
---







categories:
- Networks
- Windows


With the accuracy of a swiss clock, our windows 2000 server at work sends gratuitous ARP requests every 30 seconds for some unknown reason.

It has always been doing that. I've tried to figure out the issue every now and then, when I had some time to kill.

The question is.. is it software or hardware related ?
The strange thing is the gratuitous arp requests are supposedly from an IP totally unknown in the network.
The NIC is an Intel Pro/1000 CT.

Here's the result of a sniffing session : 



`[root@localhost]# tshark -i eth1 -n ether host 00:11:2f:a1:f4:2c    
Capturing on eth1
  0.000000 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.98?  Tell 192.168.1.2
 19.421510 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.131?  Tell 192.168.1.2
 25.506625 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
 55.542105 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
 78.000253 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.99?  Tell 192.168.1.2
 81.343247 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.43?  Tell 192.168.1.2
 85.567166 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
115.611367 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
121.155549 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.11?  Tell 192.168.1.2
145.596431 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
175.641042 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
205.675729 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
223.168944 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.43?  Tell 192.168.1.2
235.689781 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
240.000729 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.98?  Tell 192.168.1.2
265.687874 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
295.709376 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
325.765076 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
352.883391 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.11?  Tell 192.168.1.2
355.809839 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP
378.086264 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.168.1.43?  Tell 192.168.1.2
385.764773 00:11:2f:a1:f4:2c -> ff:ff:ff:ff:ff:ff ARP Who has 192.0.0.65?  Gratuitous ARP`

If someone has a clue, any idea is welcomed !

:?

Edit :
http://www.commentcamarche.net/forum/affich-2015827-gratuitous-arp

Apparently, the Intel NIC might be the cause of the issue.
I installed wireshark on the windows box, but I need to reboot our server in order to get it working.. oh well...
