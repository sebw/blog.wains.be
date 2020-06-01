---
date: 2013-07-17
title: "Debian network interface configuration"
---
Tags: Linux, Debian
Date: 2013-07-17

This is a reminder as I always seem to forget something.

/etc/network/interfaces:

	auto eth0
	iface eth0 inet static
		pre-up iptables-restore < /etc/iptables.conf
		address 192.168.0.0
		netmask 255.255.0.0
		network 192.168.0.0
		broadcast 192.168.255.255
		gateway 192.168.0.1
