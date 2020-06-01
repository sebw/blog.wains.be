---
date: 2016-03-23
title: "Simulating latency and other network issues on Linux"
---

If for some reason you need to simulate latency, latency, loss, duplication or re-ordering on a linux machine, use netem (command tc)  as root 

**Adding** a delay of 100ms on interface eth0:

    tc qdisc add dev eth0 root netem delay 100ms

**Changing** the rule:

    tc qdisc change dev eth0 root netem delay 150ms

**Removing** the rule is a simple:

    tc qdisc del dev eth0 root netem delay 100ms

Simulate a delay of 100ms with a variation or plus or minus 10ms:

    tc qdisc change dev eth0 root netem delay 100ms 10ms

Simulate packet loss:

    tc qdisc change dev eth0 root netem loss 0.1%
	
Simulate packet deduplication:

    tc qdisc change dev eth0 root netem duplicate 1%
	
Simulate packet reordering:

    tc qdisc change dev eth0 root netem delay 10ms reorder 25% 50%

More info: [https://wiki.linuxfoundation.org/networking/netem](https://wiki.linuxfoundation.org/networking/netem)
