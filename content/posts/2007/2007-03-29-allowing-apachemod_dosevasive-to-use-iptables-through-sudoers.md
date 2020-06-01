---
date: 2007-03-29
title: "Allowing Apache/mod_dosevasive to use iptables through sudoers"
---







categories:
- Apache
- Howto
- Linux
- Security


**What is mod_dosevasive ?**

mod_dosevasive is an evasive maneuvers module for Apache to provide evasive
action in the event of an HTTP DoS or DDoS attack or brute force attack.  It
is also designed to be a detection tool, and can be easily configured to talk
to ipchains, firewalls, routers, and etcetera.

Detection is performed by creating an internal dynamic hash table of IP
Addresses and URIs, and denying any single IP address from any of the following:

- Requesting the same page more than a few times per second
- Making more than 50 concurrent requests on the same child per second
- Making any requests while temporarily blacklisted (on a blocking list)

**The issue**

Apache by default runs as an unprivileged user.
When using the module dosevasive, you can set it up to trigger a command when a DoS/DDoS/Brute force attack is detected.

This is my config :

**mod_dosevasive config :**


    
    <code>LoadModule dosevasive20_module modules/mod_dosevasive20.so
    
        DOSHashTableSize    3097
        DOSPageCount        2
        DOSSiteCount        50 
        DOSPageInterval     1
        DOSSiteInterval     1
        DOSBlockingPeriod   10
    # Optional Directives - /usr/share/doc/mod_dosevasive/README for more info
        DOSEmailNotify      admin@domain.be
        DOSWhitelist        192.168.1.*
        DOSSystemCommand    "sudo /sbin/iptables -A INPUT -s %s -j DROP"
    </code>



You can see I set up dosevasive to drop the offending IP's when the system is triggered. 

As we are running apache as an unprivileged user, when need to allow apache to use sudo in order to drop the IP's with iptables.


Edit the sudoers file :
`visudo`


Add this :
`apache ALL=(ALL) NOPASSWD: /sbin/iptables -A INPUT -s [0-9.]* -j DROP`

This would allow apache/mod_dosevasive to drop an offending IP in the firewall.

As I'm not a sudoers regular user, this may not be the best recipe.. If you have a better solution, drop me a line ! :)


