---
date: 2016-03-17
title: "Manipulating network stream with netsed"
---

[Netsed page](http://silicone.homelinux.org/projects/netsed/)

Netsed is a network packet stream editor.

On a Linux gateway, let's set a silent redirect to the port netsed will be listening on:

    iptables -t nat -A PREROUTING -p tcp --dport 80 -j REDIRECT --to-ports 1080
    
Then start netsed:

    netsed tcp 1080 0 80 's/string%20A/string%20B/'
    
Any "string A" in a web page will be replaced by "string B".

In the output you would see this:

	[+] Caught server -> client packet.    Applying rule s/string%20A/string%20B...	[*] Done 1 replacements, forwarding packet of size 1453 (orig 1452).
