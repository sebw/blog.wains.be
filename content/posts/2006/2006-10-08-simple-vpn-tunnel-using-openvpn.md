---
date: 2006-10-08
title: "A simple OpenVPN tunnel to your RHEL/CentOS server"
---







categories:
- Howto
- Linux
- Security
- VPN


I'll explain how I used a static key configuration to get a simple VPN tunnel to connect to my samba share at home from work.



**OpenVPN GUI for Windows :**
Download at http://openvpn.se/download.html

I don't know any good GUI for Linux, I simply use the CLI (install the package openvpn for your distribution)

**OpenVPN 2 Server :**
Grab the latest version for your distribution at [http://dag.wieers.com/packages/openvpn/](http://dag.wieers.com/packages/openvpn/)

**Generate your static key :**

On the client or server side :
openvpn --genkey --secret static.key

You would share the static key between the server and the client over a secure channel (scp, pgp, etc.)

From official documentations, the pros and cons :

Static Key advantages

    * Simple Setup
    * No X509 PKI (Public Key Infrastructure) to maintain

Static Key disadvantages

    * Limited scalability -- one client, one server
    * Lack of perfect forward secrecy -- key compromise results in total disclosure of previous sessions
    * Secret key must exist in plaintext form on each VPN peer
    * Secret key must be exchanged using a pre-existing secure channel

As you notice, it fits to very basic needs, if you need something more complex, this is not the good place :)

**Configuration on the server side :**

Create a file /etc/openvpn/server.opvn :
`dev tun
ifconfig 10.0.0.1 10.0.0.2
secret /etc/openvpn/static.key
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
verb 1`

This configuration will make openvpn run as a daemon, using a tun device, under the user nobody. It will log connections and keep a stable connection (I stay connected hours long)

**Configuration on the client side :**

Create a file /etc/openvpn/client.opvn or c:program filesopenvpn..... :
`remote server.domain.be
dev tun
port 1194
proto udp
comp-lzo
ifconfig 10.0.0.2 10.0.0.1
secret /etc/openvpn/static.key
route 192.168.1.0 255.255.255.0`

The client will connect to the server located at server.domain.be on port 1194.
The client IP will be 10.0.0.2. After a successful connection, you should be able to ping 10.0.0.1

**Firewall configuration on the server side :**

Open port UDP/1194 under iptables
iptables -A INPUT -i tun0 -p udp -m udp --dport 1194 -d 0/0 -j ACCEPT

**Finally...**
On the server side : 

Type : 
chkconfig openvpn on
service openvpn start

OpenVPN should start listening on 10.0.0.1

Verify using the command ifconfig, search for device tun0

`tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
          inet addr:10.0.0.1  P-t-P:10.0.0.2  Mask:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:100
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)`

You should set up your services on your server to listen to this tun0 interface.
If you set up samba to listen on 10.0.0.1, you would be able to connect to your shares from the client using 10.0.0.1share from your windows client




Based on http://openvpn.net/static.html
