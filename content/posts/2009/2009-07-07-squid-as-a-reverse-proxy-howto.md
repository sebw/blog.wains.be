---
date: 2009-07-07
title: "Squid as a reverse proxy howto"
---







categories:
- Howto
- Linux
- Proxy


**The setup :**

We run a virtualization server on a server in a datacenter (for example [Proxmox VE](http://pve.proxmox.com/wiki/Main_Page)), we only have 1 public IP available.
We run web servers on 2 different virtual machines inside that VM host. We want both web servers to be accessible through the public IP on port 80.

We will use the Squid Proxy to act as a "reverse proxy" ([http://en.wikipedia.org/wiki/Reverse_proxy](http://en.wikipedia.org/wiki/Reverse_proxy)).
Squid will relay the requests to the destination depending on the hostname requested.

**The machines :**

Virtualization server (VM host)/Squid server : Public IP 10.20.30.40 - bridged LAN IP 172.16.5.97/255.255.0.0
VM1 : bridged LAN IP 172.16.100.25/255.255.0.0 - Hostname example.org
VM2 : bridged LAN IP 172.16.100.122/255.255.0.0 - Hostname example.net

**On your client computer (optional if you already have domains) :**

Edit /etc/hosts and add :

10.20.30.40 example.org
10.20.30.40 example.net

**On VM1 and VM2 :**

`apt-get install lighttpd (or whatever web server you like)`

Edit /var/www/index.ligtthpd.html and replace the content of the file by "VM1" on VM1 and "VM2" on VM2.

**On the VM host :**

If Apache listens on port tcp/80, disable it by editing /etc/apache2/ports.conf and removing or commenting "Listen 80".

_Install Squid :_

`apt-get install squid`

_Edit /etc/squid/squid.conf and find the http_port section, and add "http_port 80 vhost vport" :_

`http_port 3128
http_port 80 vhost vport`

_Then add the following section :_

`cache_peer 172.16.100.25 parent 80 0 no-query originserver name=server1
cache_peer_domain server1 example.org
cache_peer 172.16.100.122 parent 80 0 no-query originserver name=server2
cache_peer_domain server2 example.net`

_And then add the following ACL for our domains :_

`acl valid_domains dstdomain .example.org
acl valid_domains dstdomain .example.net`

_Allow requests to our domains by adding "http_access allow valid_domains" just before the "deny all" rule (at the end of ACL's) :_

`http_access allow valid_domains
http_access deny all`


Restart Squid :

`/etc/init.d/squid restart`


**Back on your computer :**

Make a request on example.net or .org, you should either see VM1 or VM2 displayed in your browser depending on the hostname requested.
