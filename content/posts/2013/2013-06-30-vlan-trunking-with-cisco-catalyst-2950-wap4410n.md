---
date: 2013-06-30
title: "VLAN trunking with Cisco Catalyst 2950 + WAP4410N"
---


Tags: Networks
- VLAN 10 is WORK
- VLAN 20 is HOME
- VLAN 30 is GUEST

**On the 2950, configure the port to the WAP4410N as trunk :**

```
switch#conf t
switch(config)#interface fastEthernet 0/12
switch(config-if)#description WAP4410N
switch(config-if)#switchport trunk native vlan 30
switch(config-if)#switchport trunk allowed vlan 10,20,30
switch(config-if)#switchport mode trunk
```

By default all VLAN are allowed on a trunk. It is recommended to specify which VLAN you want on the trunk.

Native VLAN will be the VLAN of any untagged frame. This is somewhat useless here as WLAN are tagged.

**Review the configuration :**

```
switch#show interfaces trunk

Port        Mode         Encapsulation  Status        Native vlan
Fa0/12      on           802.1q         trunking      30

Port      Vlans allowed on trunk
Fa0/12      10,20,30

Port        Vlans allowed and active in management domain
Fa0/12      10,20,30

Port        Vlans in spanning tree forwarding state and not pruned
Fa0/12      10,20,30
```

**Configure the WAP4410N as such :**

![Screen Shot 2013-06-30 at 11.12.06](https://blog.wains.be/images/Screen-Shot-2013-06-30-at-11.12.06-300x172.png)

The IP in "Setup > Basic Setup" should be in the subnet of VLAN 20. It allows you to manage the unit remotely.

Under "Administration > Management" make sure you enable "Wireless web access".
