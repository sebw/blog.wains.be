---
date: 2007-02-13
title: "Tunneling UDP through SSH"
---







categories:
- Howto
- Linux
- Security
- SSH


Say you need to forward UDP packets between two remote networks securely. 
E.g : dns queries from your home machine to your dns servers at work.
You should use a VPN between the networks in order to do so (see : [http://blog.wains.be/post/routed-openvpn-between-two-subnets-behind-nat-gateways/](http://blog.wains.be/post/routed-openvpn-between-two-subnets-behind-nat-gateways/))

Otherwise, you can use the following way :



**1. Connect to the remote server and set up TCP forward**
`client$ ssh -L 22222:127.0.0.1:22222 remote.server.be`

Any request sent to your local tcp/22222 port will be tunneled securely to tcp/22222 on the remote server.

We will then use netcat to forward the TCP queries to the UDP server..


**2. TCP to UDP forward with netcat on the server**
`server$ mkfifo /tmp/fifo
server$ iptables -A INPUT -p tcp --dport 22222 -j ACCEPT
server$ nc -l -p 22222  /tmp/fifo`


**3. UDP to TCP forward with netcat on the client**
`client$ mkfifo /tmp/fifo
client$ sudo nc -l -u -p 53  /tmp/fifo`

Use sudo if you are not root, you need root access for binding services to ports under 1024.


**4. Query**
`nslookup sub.domain.be 127.0.0.1`


**Schema :**

client --> request to 127.0.0.1 udp/53 --> netcat forwarding from udp/53 to tcp/22222 --> tcp/22222 request tunneled through SSH --> server receives requests on tcp/22222 --> netcat forwarding from tcp/22222 to the specified IP address on udp/53 --> server


Based on : [http://zarb.org/~gc/html/udp-in-ssh-tunneling.html](http://zarb.org/~gc/html/udp-in-ssh-tunneling.html)
