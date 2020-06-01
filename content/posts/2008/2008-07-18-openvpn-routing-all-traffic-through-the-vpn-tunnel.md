---
date: 2008-07-18
title: "OpenVPN - routing all traffic through the VPN tunnel"
---







Categories:  
- Apache  
- Debian/Ubuntu  
- Howto  
- Linux  
- RHCE  
- Security  
- VPN  


I'm really into OpenVPN these days, see my two previous posts about it:

Setting up OpenVPN for your road warriors: 
[http://blog.wains.be/post/a-vpn-for-remote-users-with-openvpn/](http://blog.wains.be/post/a-vpn-for-remote-users-with-openvpn/)

Setting up a VPN between two sites:
[http://blog.wains.be/post/routed-openvpn-between-two-subnets-behind-nat-gateways/](http://blog.wains.be/post/routed-openvpn-between-two-subnets-behind-nat-gateways/)

**Today : how to route all traffic through the OpenVPN tunnel**

**On the server side:**

First of all, if you want to route all your traffic through the VPN tunnel, you need to turn on IP forwarding (also called routing) and add a masquerading rule on the server (where eth0 is the device connecting you to the internet):
    
    echo "1" > /proc/sys/net/ipv4/ip_forward
    iptables -t nat -A POSTROUTING -s 10.30.0.0/24 -o eth0 -j MASQUERADE



To make routing persistent, see [http://blog.wains.be/post/enable-ip-forward-under-rhelcentos/](http://blog.wains.be/post/enable-ip-forward-under-rhelcentos/)

Then, here's the OpenVPN configuration:

	port    10000
	proto   udp
	dev     tun
	comp-lzo
	ca      ca.crt
	cert    server.crt
	key     server.key
	dh      dh1024.pem
	duplicate-cn
	server  10.30.0.0 255.255.255.0
	client-to-client
	push    "dhcp-option DOMAIN local.example.org"
	push    "dhcp-option DNS 172.16.7.253"
	push    "redirect-gateway def1"
	keepalive       10 120
	persist-key
	persist-tun
	user nobody
	group nogroup
	log     vpn.log
	verb    1
	chroot /tmp

You can see the option **redirect-gateway** that is responsible for creating all the routes on the client computer when the connection is set up.

The two other push options are only taken into account by Windows clients (to my knowledge).
If you want to change the DNS resolution of your linux clients, you need to use the up and down options on the client (see below).

**Client configuration:**

vpn.conf:
    
    client
    dev tun
    proto udp
    remote vpn.example.org
    port 10000
    nobind
    comp-lzo
    persist-key
    persist-tun
    ca ./ca.crt
    cert ./user.crt
    key ./user.key 
    verb 5
    up ./up.sh
    down ./down.sh
    ping 60
    ping-restart 120



up.sh:

	#!/bin/sh
	mv /etc/resolv.conf /etc/resolv.conf.bak
	echo "search local.example.org" > /etc/resolv.conf
	echo "nameserver 172.16.7.253" >> /etc/resolv.conf

down.sh:

	#!/bin/sh
	mv /etc/resolv.conf.bak /etc/resolv.conf


When connecting to the server (with verbose option set to 5), we can see the server pushing the route settings to the client.

	Fri Jul 18 23:22:19 2008 us=838005 ifconfig tun0 10.30.0.6 pointopoint 10.30.0.5 mtu 1500
	Fri Jul 18 23:22:19 2008 us=843211 route add -net 72.x.x.x netmask 255.255.255.255 gw 172.16.7.253
	Fri Jul 18 23:22:19 2008 us=845178 route add -net 0.0.0.0 netmask 128.0.0.0 gw 10.30.0.5
	Fri Jul 18 23:22:19 2008 us=848568 route add -net 128.0.0.0 netmask 128.0.0.0 gw 10.30.0.5
	Fri Jul 18 23:22:19 2008 us=850460 route add -net 10.30.0.0 netmask 255.255.255.0 gw 10.30.0.5


On the client, the routes :


    
    <code>$ route -n
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    72.x.x.x  172.16.7.253   255.255.255.255 UGH   0      0        0 wlan0
    10.30.0.5       0.0.0.0         255.255.255.255 UH    0      0        0 tun0
    172.16.7.0     0.0.0.0         255.255.255.0   U     0      0        0 wlan0
    10.30.0.0       10.30.0.5       255.255.255.0   UG    0      0        0 tun0
    0.0.0.0         10.30.0.5       128.0.0.0       UG    0      0        0 tun0
    128.0.0.0       10.30.0.5       128.0.0.0       UG    0      0        0 tun0
    0.0.0.0         172.16.7.253   0.0.0.0         UG    0      0        0 wlan0</code>



