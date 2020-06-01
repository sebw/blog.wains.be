---
date: 2007-10-05
title: "DHCP snooping on Cisco Catalyst 2950"
---







categories:
- Hardware
- Networks
- Security


I'll explain the few commands used to enable DHCP snooping on our Catalyst switch at work.
The users aren't really tech savvy or anything, but it is painless to configure so it is worth having it enabled.

switch1>enable

**First, we need to know which VLAN the DHCP server belongs to, "show arp" has that info :**

switch1#show arp
Protocol  Address          Age (min)  Hardware Addr   Type   Interface
Internet  192.168.1.3           1   0004.75XXXXXX  ARPA   Vlan1

**Then we enable configuration mode :**

switch1#conf term

**Enabling DHCP snooping on VLAN 1 :**

switch1(config)#ip dhcp snooping
switch1(config)#ip dhcp snooping vlan 1
switch1(config)#ip dhcp snooping information option

**The DHCP server is connected to the second port, we will add that interface to the trusted list  :**

switch1(config)#interface fastEthernet 0/2
switch1(config-if)#ip dhcp snooping trust

**Let's review the config :**

switch1#show ip dhcp snooping 
Switch DHCP snooping is enabled
DHCP snooping is configured on following VLANs:
1
Insertion of option 82 is enabled
Interface                    Trusted     Rate limit (pps)
------------------------     -------     ----------------
FastEthernet0/2              yes         unlimited


DHCP snooping is enabled on VLAN 1 for interface 2 !

**Saving the config :**

switch1#copy running-config startup-config
