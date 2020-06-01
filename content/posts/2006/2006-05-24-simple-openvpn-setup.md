---
date: 2006-05-24
title: "Simple OpenVPN setup"
---

This will explain how to setup a simple OpenVPN tunnel between two computers (at a time). If someone attempts to connect while another person is already connected, that person will get bounced from the VPN tunnel..

On the server-side :

1. yum install openvpn

2. edit /etc/openvpn/server.conf 

		dev tun
		ifconfig 10.0.0.1 10.0.0.2
		secret static.key
		port 1194
		proto udp
		user nobody
		group nobody
		daemon
		comp-lzo
		keepalive 10 60
		ping-timer-rem
		persist-tun
		persist-key
		log /var/log/openvpn.log
		verb 1

3. openvpn --genkey --secret /etc/openvpn/static.key

4. share the static.key file with the client over a secure channel (gpg crypted, scp,...)

5. open port udp/1194 under your NAT/firewall/any security appliance

On the client-side :

1. Linux : yum install openvpn
Windows : install OpenVPN client from [http://www.openvpn.se/](http://www.openvpn.se/)

2. Edit client.conf and put it either under /etc/openvpn/ for linux or under the conf directory under Windows

		remote remote.hostname.be or remote IP
		dev tun
		port 1194
		proto udp
		comp-lzo
		ifconfig 10.0.0.2 10.0.0.1
		secret static.key
		route 192.168.100.0 255.255.255.0 (optional)

3. Make sure static.key is on the client machine

See [http://www.openvpn.se/screenshots.html](http://www.openvpn.se/screenshots.html) for screenshots

4. You should now be able to connect to the VPN and ping 10.0.0.1 unless your security setup doesn't allow it to

5. If you need to reach the 192.168.100.0/24 subnet on the server to access certains services, add the line "route 192.168.100.0 255.255.255.0" to your client config file.. Depending on your firewall configuration you may need to allow ip forwarding from the incoming interface (tun0) to the internal interface (let's say eth1)..

This is how I did it :
`iptables -A FORWARD -i eth1 -o tun0 -m state --state NEW,ESTABLISHED,RELATED -j ACCEPT
iptables -A FORWARD -i tun0 -o eth1 -m state --state ESTABLISHED,RELATED -j ACCEPT`

Info : [http://openvpn.net/static.html](http://openvpn.net/static.html)
