---
date: 2009-04-23
title: "Howto - setting up dns2tcp"
---







categories:
- DNS
- Howto
- Wifi


_The following article has been tested on Debian Etch (server) and Debian Lenny and Mac OS X (clients)._

Edit 03/2011 : dns2tcp client v0.5 won't work with dns2tcp server v0.4. 

I'm not gonna explain what dns2tcp is, just how to get it running in less than 30 minutes.


**You need :**
- a public server, reachable from anywhere, its UDP/53 port must be free (no DNS service running) and reachable (not filtered)
- a domain name or subdomain dedicated for dns2tcp
- a dns2tcp client computer, your laptop usually
- a "restricted" network (captive portal, firewalled network, paying hotspot) allowing DNS requests (in our examples, we'll be using Google DNS server 8.8.8.8, but you can try to use the LAN DNS)


**Considerations :**
dns2tcp public server IP : srv1.example.org (IP 1.2.3.4)
dns2tcp subdomain : tunnel.example.org (this doesn't need an A record, just a NS record pointing to srv1.example.org)
dns2tcp resources (services which dns2tcp will make available to us) : 
- SSH on TCP/22 at srv1.example.org (same machine as dns2tcp)
- SSH on TCP/22 at srv2.example.org (1.2.3.5)


**DNS : **
Create a NS record for the subdomain tunnel.example.org pointing to address srv1.example.org

In ISC BIND :
`tunnel.example.org.        IN      NS      srv1.example.org.`

The NS you specify is NOT a DNS server, it's the dns2tcp server !

Hint : everydns.net allows creation of NS records for subdomains. Not all control panels do (Enom for example).


**Server :**
Install dns2tcp on srv1.example.org (apt-get install dns2tcp on Debian)
Edit the file /etc/dns2tcpd.conf like this :

`listen = 0.0.0.0
port = 53
user = nobody
chroot = /some/directory/
domain = example.org
ressources = ssh-home:127.0.0.1:22 , ssh-work:srv2.example.org:22`

Start dns2tcp server with /etc/init.d/dns2tcp start
Make sure it's running with ps and listening with netstat.


**Client :**
Don't forget the client must already be installed on your computer when you are on the restricted network :-)
Install it right now : apt-get install dns2tcp on Debian or build it through macports on Mac OS X.

Connect to the restricted network.

Run the command :
`dns2tcpc -z example.org 8.8.8.8`

If the system is working you should see :
Available connection(s) : 
ssh-home
ssh-work


Run the full command now :
`dns2tcpc -z example.org -l 12345 -r ssh-home 8.8.8.8`

8.8.8.8 is the DNS server that will relay the encapsulated DNS requests to our dns2tcp server.
If the network restricts the use of external DNS servers, check your /etc/resolv.conf to get the DNS servers on the local network.

Now dns2tcp will listen on port TCP/12345 (option "-l 12345") and will give you access to the resource "ssh-home" through that port.

Now connect to your SSH server through dns2tcp on port TCP/12345 :
`ssh user@localhost -p 12345 -D 1080`

You should connect to your home server !

The "-D 1080" option will create a SOCKS proxy on your local machine on port TCP/1080.

Now set up your browser or any other program (like Pidgin if you want to chat) to use the SOCKS proxy at address 127.0.0.1 and port 1080.
You can also set the systemwide parameter for SOCKS proxy from the preferences panel of your OS.

You should now be able to browse the internet.


You can store a config file on the client computer if you don't want to type the command everytime.. this is the config corresponding to the command :

/home/USER/.dns2tcprc :

`domain = example.org
ressource = ssh-home
local_port = 12345
server = 8.8.8.8`

This way, you just need to run dns2tcpc without argument.
If you store the config file somewhere else, run dns2tcpc -f /where/the/config/resides/dns2tcp.conf


**Please note :**
Your traffic is encapsulated inside small DNS packets (some firewalls can drop unusually large DNS packets), is encrypted because of SSH, etc.
This adds overhead, which makes browsing the web a bit slow but still convenient. 
I've been able to reach 25 KB/s down and 20 KB/s BUT I haven't been able to transfer large files though, it was taking forever to attach a 3 MB pictures to a mail in Gmail (wifi + UDP + small packets is a terrible mix)
A good idea is to use mobile versions of websites, they load faster.
To give you an idea, it can take up to a minute to display maps on Google Maps.
Since you are going through the SOCKS proxy created by the SSH connection, your traffic is encrypted and wifi users can't snoop on you.
Obviously you can define anything as a resource in dns2tcp, for example you can point to a public web proxy but your traffic wouldn't be encrypted !
The owner of the restricted network may notice unusually high DNS traffic while you are surfing (especially if you're the only person using the wifi network in the hotel).
