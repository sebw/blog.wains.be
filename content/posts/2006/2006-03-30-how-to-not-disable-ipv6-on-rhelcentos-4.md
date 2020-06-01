---
date: 2006-03-30
title: "How to NOT disable IPv6 on RHEL/CentOS 4"
---

There's a myth on the web surrounding the method on how to disable IPv6 under RHEL/CentOS 4

Adding "NETWORKING_IPV6=no" to /etc/sysconfig/network **DOES NOT** work

If you want to disable IPv6, the only true working trick is : 
`echo "alias net-pf-10 off" >> /etc/modprobe.conf`

Period
