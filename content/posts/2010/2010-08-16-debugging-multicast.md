---
date: 2010-08-16
title: "Debugging Multicast"
---







categories:
- Networks


On the "client" side :
`iperf -u -c 239.192.95.98 -p 10000 -b 1 -i 5 -T 10 -t 120`

-u : use UDP
-c : run in client mode 
-p : port
-b : bandwidth in bits/sec
-i : interval in second
-T : TTL
-t : time to transmit in seconds

On the "server" side :
`iperf -s -i 1 -u -B 239.192.95.98 -p 10000`

-s : run in server mode 
-B : bind to multicast address
