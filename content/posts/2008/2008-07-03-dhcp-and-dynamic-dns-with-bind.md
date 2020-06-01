---
date: 2008-07-03
title: "DHCP and Dynamic DNS with BIND"
---







categories:
- Debian/Ubuntu
- DNS
- Howto
- Linux
- Networks


_Tested under Debian Etch_

This is the network configuration of our DHCP/DNS server

Hostname : router.static.example.org
WAN interface (eth0) : 192.168.99.254 mask 255.255.255.0
LAN interface (eth1) : 172.30.200.254 mask 255.255.0.0
Default gateway : 192.168.99.1


First, we need to tell the DHCP server to only run on eth1 :

**/etc/default/dhcp3-server :**

`INTERFACES="eth1"`


**/etc/dhcp3/dhcpd.conf :**

This is the DHCP server configuration.

When a computer requests network information from the DHCP server, the DHCP will update the DNS zones
- dyn.example.org : the zone that will map hostnames to IP address
- 201.30.172.in-addr.arpa : the zone in charge of reverse lookups

ddns-domainname is the domain name that the DHCP server will try to update in the zone. For example if my computer is named mycomputer, it will try to upload the dyn.example.org zone with mycomputer.dyn.example.org.

That option is absolutely needed if you have several domains in the "option domain-name" field (the "search" domains that will be in /etc/resolv.conf), or it could try to add the hostname mycomputer.static.example.org to the dyn.example.org zone.

If you only have one domain in the "option domain-name" field, you can go without ddns-domainname as it will upload the zone with the domain specified there.



    
    <code>ddns-update-style interim;
    include "/etc/bind/rndc.key";
    
    zone dyn.example.org. {
            primary 127.0.0.1; 
            key "rndc-key";
    }
    
    ddns-domainname "dyn.example.org";
    option domain-name "static.example.org dyn.example.org";
    option domain-name-servers 172.30.200.254;
    option routers 172.30.200.254;
    option broadcast-address 172.30.255.255;
    option ntp-servers 172.30.200.254;
    
    default-lease-time 86400;
    max-lease-time 86400;
    
    authoritative;
    
    log-facility local7;
    
    subnet 172.30.0.0 netmask 255.255.0.0 {
    
            range 172.30.201.10 172.30.201.200;
    
            # DNS zones to update
            zone 201.30.172.in-addr.arpa. {
                    primary 172.30.200.254; 
                    key "rndc-key";
            }
    
            zone dyn.example.org. {
                    primary 172.30.200.254; 
                    key "rndc-key";
            }
    }</code>





**/etc/bind9/named.conf :**

Make sure the file contains the following :
`include "/etc/bind/named.conf.local";`

You should not change that file, as you will specify your options in two other files.


**/etc/bind9/named.conf.options :**

Your options.
The zone files will be stored under /var/cache/bind/
The queries for unauthoritative domains will be forwarded to 192.168.99.1. You can put the DNS provided by your ISP there (or put the DNS from opendns.com)


    
    <code>options {
            directory "/var/cache/bind";
    
            query-source address * port 53;
    
            forwarders {
                    192.168.99.1;
            };
    
            recursion yes;
    
            version "REFUSED";
    
            allow-recursion {
                    127.0.0.1;
                    192.168.99.0/24;
                    172.30.0.0/16;
            };
    
            allow-query {
                    127.0.0.1;
                    192.168.99.0/24;
                    172.30.0.0/16;
            };
    
    };</code>





**/etc/bind9/named.conf.local :**

This will contain your zone declarations


    
    <code>### options #########
    
    include "/etc/bind/rndc.key";
    controls {
            inet 127.0.0.1 allow { localhost; } keys { "rndc-key"; };
    };
    
    ### "static" zones #########
    
    zone "static.example.org" {
            type master;
            file "db.static.example.org";
    };
    
    zone "200.30.172.in-addr.arpa" {
            type master;
            notify no;
            file "db.172.30.200";
    };
    
    
    ### dynamic zones (updated by DDNS) #########
    
    zone "dyn.example.org" {
            type master;
            file "db.dyn.example.org";
            allow-update { key "rndc-key"; };
    };
    
    zone "201.30.172.in-addr.arpa" {
            type master;
            notify no;
            file "db.172.30.201";
            allow-update { key "rndc-key"; };
    };</code>




**Now let's focus on DNS zones.**

In this example we have several zones :

- static.example.org : static zone (like servers with static IP's)
- dyn.example.org : dynamic zone, updated by DHCP when a computer gets an IP from it
- 172.30.200 : static zone (servers, etc.), which is not updated by DDNS
- 172.30.201 : dynamic zone, will contain information about machines using DHCP

I advise to split the static zones from the dynamic zones, DDNS has a tendency to mess up the zone files, which make them barely readable and manageable.


**/var/cache/bind/db.172.30.200 :**


    
    <code>$ORIGIN .
    $TTL 86400	; 1 day
    200.30.172.in-addr.arpa	IN SOA	static.example.org. postmaster.example.org. (
    				200806299  ; serial
    				28800      ; refresh (8 hours)
    				7200       ; retry (2 hours)
    				2419200    ; expire (4 weeks)
    				86400      ; minimum (1 day)
    				)
    			NS	ns.static.example.org.
    $ORIGIN 200.30.172.in-addr.arpa.
    253			IN PTR	server.static.example.org.
    254			IN PTR	router.static.example.org.</code>




**/var/cache/bind/db.172.30.201 :**


    
    <code>$ORIGIN .
    $TTL 86400	; 1 day
    201.30.172.in-addr.arpa	IN SOA	static.example.org. postmaster.example.org. (
    				200806327  ; serial
    				28800      ; refresh (8 hours)
    				7200       ; retry (2 hours)
    				2419200    ; expire (4 weeks)
    				86400      ; minimum (1 day)
    				)
    			NS	ns.static.example.org.
    $ORIGIN 201.30.172.in-addr.arpa.</code>




**/var/cache/bind/db.static.example.org :**


    
    <code>$ORIGIN .
    $TTL 86400	; 1 day
    static.example.org	IN SOA	ns.static.example.org. postmaster.example.org. (
    				200806327  ; serial
    				28800      ; refresh (8 hours)
    				7200       ; retry (2 hours)
    				2419200    ; expire (4 weeks)
    				86400      ; minimum (1 day)
    				)
    			NS	ns.static.example.org.
    			A	172.30.200.254
    $ORIGIN static.example.org.
    server			A	172.30.200.253
    router			A	172.30.200.254
    ns			A	172.30.200.254
    
    ntp			CNAME	router.static.example.org. 
    smtp			CNAME	router.static.example.org.</code>




**/var/cache/bind/db.dyn.example.org**


    
    <code>$ORIGIN .
    $TTL 86400	; 1 day
    dyn.example.org		IN SOA	ns.static.example.org. admin.example.org. (
    				200806341  ; serial
    				28800      ; refresh (8 hours)
    				7200       ; retry (2 hours)
    				2419200    ; expire (4 weeks)
    				86400      ; minimum (1 day)
    				)
    			NS	ns.dyn.example.org.
    			A	172.30.200.254
    $ORIGIN dyn.example.org.</code>




**Now, make sure the zones will be writable by the user "bind" and restart the services :**

`# chown bind. /var/cache/bind/*


**On a computer on the network :**

As root :

Edit /etc/dhcp3/dhclient.conf and set :
`send host-name "mycomputer";`

Now request an IP :
`# dhclient eth0`

Let's imagine the computer has received the IP 172.30.201.200

You should see on the server's syslog that the DNS zones have been updated.

- mycomputer.dyn.example.org is now bound to 172.30.201.200
- 172.30.201.200 will return mycomputer.dyn.example.org

From your computer, you should be able to verify the zones have been updated properly :


    
    <code>$ nslookup mycomputer
    Server:		172.30.200.254
    Address:	172.30.200.254#53
    
    Name:	mycomputer.dyn.example.org
    Address: 172.30.201.200</code>





    
    <code>$ nslookup 172.30.201.200
    Server:		172.30.200.254
    Address:	172.30.200.254#53
    
    200.201.30.172.in-addr.arpa	name = mycomputer.dyn.example.org.</code>



You don't need to type the whole mycomputer.dyn.example.org thing since it will lookup for either :
- mycomputer.dyn.example.org
- mycomputer.static.example.org if the previous wasn't found
- mycomputer if the previous two were not found

This actually means that if you lookup www.google.be, it would try to resolve www.google.be.dyn.example.org first, then www.google.be.static.example.org, and finally www.google.be

You can avoid those unnecessary lookups by adding a dot to the end of the hostname you are trying to resolve :

`# nslookup www.google.be.`

This is the purpose of the search domains in /etc/resolv.conf

Pay attention to the syntax in /etc/resolv.conf.
You should be able to specify up to 6 domains to look up automatically.
Each domains should be on one line, separated by spaces.
I made a mistake and had domains separated by commas. 
It was working perfectly fine with up to 2 domains on the search line.
With 3 domains or more, the computer would just not try to resolve on the domains at all. 


