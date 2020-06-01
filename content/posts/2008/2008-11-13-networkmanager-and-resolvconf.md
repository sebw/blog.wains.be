---
date: 2008-11-13
title: "NetworkManager and resolv.conf"
---







categories:
- Debian/Ubuntu
- Gnome
- Linux
- Networks
- VPN


I'm under Debian Lenny at work.

I installed the VPNC plugin (Cisco VPN) for NetworkManager today.
I usually always connect to OpenVPN tunnels with the OpenVPN plugin, which works pretty well (at least on NM 0.6.6).

After installing the VPNC plugin, I started having issues with my OpenVPN tunnels.. I was not able to resolve remote hostnames correctly.

The problem was that my resolv.conf didn't get updated upon connection to the OpenVPN.

When installing network-manager-vpnc-gnome, a dependency named "resolvconf" got installed along. The package somewhat protected /etc/resolv.conf from being updated.

After getting rid of resolvconf, my OpenVPN VPNs are working again !
Also, this didn't prevent the Cisco VPN from working..

I need to check out what that resolvconf package is all about.. if someone has a clue, please comment :)
