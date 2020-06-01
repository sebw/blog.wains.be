---
date: 2007-02-28
title: "Setting up NIS under CentOS 4"
---







categories:
- RHCE


Server : server.lab.local (10.0.0.254)
Client : client1.lab.local (10.0.0.1)



**Server side :**

Packages needed : yp-tools ypbind ypserv portmap



**Edit /etc/yp.conf :**
`domain lab.local server server
ypserver server`

Following this scheme : 
domain ${domain} server ${host}
ypserver ${host}

**By default /etc/ypserv.conf is ok.**

**Edit /etc/sysconfig/network and add :**
`NISDOMAIN=lab.local`

**At the prompt :**
`# domainname lab.local

**Still under the prompt :**
`service portmap start
chkconfig portmap on`

**Start the NIS server :**
`service ypserv start`

**Make sure it's actually running : **
`# rpcinfo -u localhost ypserv`

Output should look like :
`program 100004 version 1 ready and waiting
program 100004 version 2 ready and waiting`

Build the NIS maps :
`/usr/lib/yp/ypinit -m`
There you should specify the machines that will run a NIS server, when done hit ctrl + D

**Start ypbind, yppasswdd, ypxfrd :**
`# service ypbind start

**Make sure the required services will start at boot : **
`for service in ypserv ypbind yppasswdd ypxfrd; do chkconfig $service on; done`



**Client side :**

Packages needed : yp-tools ypbind portmap

**At the prompt :**
`# authconfig`
or
`# system-config-authentication`

There you should select "Use NIS" then select "Next"
Then fill in the required info :
Domain : lab.local
Server : server.lab.local

Click OK

Your client machine should be properly configured and attached to the NIS server now.
Verify with :
`client1# rpcinfo -u localhost ypbind`

Then, check the NIS server map with :
`client1# ypcat passwd`

You should see the list of accounts on the NIS server.

**Make sure the needed services will start at boot on the client :**
`for service in portmap ypbind; do chkconfig $service on; done`

**Edit /etc/hosts and add :**
`10.0.0.254 server.lab.local server`
This is very important, for some reason, after setting up NIS auth, when pinging "server.lab.local" it was actually pinging the localhost. (if someone has a clue as to why it does that, drop me a line. I sniffed the traffic and noticed some NIS traffic when trying to ping the server)



**Now, back to the server side !**

**Adding new users to the NIS server :**

`server# useradd account
server# passwd account
server# cd /var/yp
server# make`

Make will update the NIS maps


More info :
http://cern91.tuxfamily.org/linux/indexnet.php?page=nis
http://bradthemad.org/tech/notes/redhat_nis_setup.php
