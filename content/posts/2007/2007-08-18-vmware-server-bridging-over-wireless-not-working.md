---
date: 2007-08-18
title: "VMWare Server - bridging over wireless not working"
---







categories:
- Wifi


I was not able to get the bridging network mode working for my virtual machines under VMWare Server.
Then, I figured out I was using the eth1 interface (the wireless interface), while the bridge mode was probably bound to eth0 (wire interface).

**This is how to fix the issue :**

$ sudo vmware-config-network.pl

Would you like to skip networking setup and keep your old settings as they are?
(yes/no) [yes] **no**

Do you want networking for your virtual machines? (yes/no/help) [yes] **yes**

Would you prefer to modify your existing networking configuration using the 
wizard or the editor? (wizard/editor/help) [wizard] w 

The following bridged networks have been defined:

. vmnet0 is bridged to eth0

Do you wish to configure another bridged network? (yes/no) [no] **yes**

Configuring a bridged network for vmnet2.

Your computer has multiple ethernet network interfaces available: eth1, vmnet1,
vmnet8. Which one do you want to bridge to vmnet2? [eth0] **eth1**

The following bridged networks have been defined:

. vmnet0 is bridged to eth0
. vmnet2 is bridged to eth1

Voilà, now set up your virtual machine to use vmnet2 as networking device, the virtual machine will now be a part of your LAN.

