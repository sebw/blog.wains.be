---
date: 2008-06-06
title: "Routed OpenVPN between two subnets behind NAT gateways"
---







categories:
- Networks
- Security
- VPN


**Edit : between two or MORE subnets. Check out the exchange between Michael Antal and me in the comments. He's been able to interconnect 3 subnets using this method and some slight tweaks in routes.**

The following is the configuration needed to create a routed OpenVPN network between two remote subnets, both behind NAT gateways. On each side, the gateways will act as the VPN gateways.

Network subnets on both side must be different. In this example, we connect 192.168.1.0/24 to 192.168.200.0/24.

With this setup, we don't even need to open ports at the NAT level on either side of the VPN.


**Network configuration**

Network A :

Subnet : 192.168.1.0/24

	Gateway for Network A
	VPN interface (tun0) : 10.0.0.1
	Eth0 : 192.168.1.254
	Eth1 / Public IP : 212.x.x.x (machineA.example.org)


Network B :

Subnet : 192.168.200.0/24

	Gateway for Network B
	VPN interface (tun0) : 10.0.0.2
	Eth0 / Public IP : 66.x.x.x (machineB.example.org)
	Eth1 : 192.168.200.254


If we imagine forwarding is rejected at the firewall level on machine B, we would need to allow traffic between the tun device created by OpenVPN and the LAN interface, as root you would then type :

	iptables -I FORWARD -i tun0 -o eth1 -j ACCEPT
	iptables -I FORWARD -i eth1 -o tun0 -j ACCEPT

Make sure the rules remain applied across reboots (using iptables-save or storing the rules in /etc/rc.local or else)



**Enabling IP routing :**

We need to enable routing on both VPN gateways..

	echo "1" > /proc/sys/net/ipv4/ip_forward

Using this method, routing is not persistent across reboots, check the following [link](https://blog.wains.be/2006/2006-06-06-enable-ip-forward-under-rhelcentos.md) (which works for Debian as well)



**Static key generation :**

We will use simple static key for our example...

`openvpn --genkey --secret /etc/openvpn/vpn.key`

Share the key between the VPN gateways over a secure channel (scp, etc.).

If you want to use certificates instead (which I recommend), check out : [http://blog.wains.be/post/a-vpn-for-remote-users-with-openvpn/](http://blog.wains.be/post/a-vpn-for-remote-users-with-openvpn/)



**OpenVPN configuration :**

machine A : /etc/openvpn/vpn.conf

	remote machineB.example.org
	float
	port 8000
	dev tun 
	ifconfig 10.0.0.1 10.0.0.2 
	persist-tun 
	persist-local-ip 
	persist-remote-ip 
	comp-lzo 
	ping 15
	secret /etc/openvpn/vpn.key 
	route 192.168.200.0 255.255.255.0
	chroot /var/empty
	user nobody
	group nogroup
	# If using Red Hat replace with
	# group nobody 
	log vpn.log
	verb 1



machine B : /etc/openvpn/vpn.conf

	remote machineA.example.org 
	float 
	port 8000
	dev tun 
	ifconfig 10.0.0.2 10.0.0.1
	persist-tun
	persist-local-ip
	persist-remote-ip
	comp-lzo
	ping 15
	secret /etc/openvpn/vpn.key
	route 192.168.1.0 255.255.255.0
	chroot /var/empty 
	user nobody
	group nogroup
	# If using Red Hat replace with
	# group nobody 
	log vpn.log
	verb 1



**Establishing the connection :**

On machine A type :

`openvpn --config /etc/openvpn/vpn.conf`

Machine A will try to establish the connection. Type the same command on machine B to initialize the connection.

As soon as the connection is up, hosts on network A should be able to ping hosts on network B and vice-versa. If you can't ping from one side to the other, it's probably a routing issue.

We can use traceroute from a machine on network A to see the path taken to reach a host on network B :

`$ traceroute 192.168.200.30
traceroute to 192.168.200.30 (192.168.200.30), 30 hops max, 40 byte packets
 1  192.168.1.254 (192.168.1.254)  2.128 ms  2.378 ms  2.781 ms
 2  10.0.0.2 (10.0.0.2)  132.761 ms  137.342 ms  141.130 ms
 3  192.168.200.30 (192.168.200.30)  136.752 ms  133.869 ms  135.960 ms`

We can see the traffic is going through the 10.0.0.2 interface.

We also can check the routing table on the VPN gateway on network A :

`# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
10.0.0.2        0.0.0.0         255.255.255.255 UH    0      0        0 tun0
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
192.168.200.0   10.0.0.2        255.255.255.0   UG    0      0        0 tun0
0.0.0.0         212.x.x.x   0.0.0.0         UG    0      0        0 eth1`

We see any traffic directed to 192.168.200.0/255.255.255.0 must go through interface 10.0.0.2 on device tun0.

**Making sure the VPN connection is established at boot (Debian way) :**

Edit /etc/defaults/openvpn and specify which VPN network must be started.

Make OpenVPN starts at boot with this command : 

`update-rc.d openvpn defaults`

OpenVPN will start any VPN configuration named *.conf found in /etc/openvpn/
So don't store copies of config like test.conf test-backup.conf as OpenVPN will try to start them at boot.
