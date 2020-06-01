---
date: 2007-06-07
title: "Blocking Internet Explorer with the Squid Web proxy"
---







categories:
- Howto
- Linux
- Proxy


This is my own way of blocking Internet Explorer :

/etc/squid/squid.conf :


    
    <code>### We want to block IE, but some sites (grr) are only working under IE
    ### so we put up a list of safe URL for Internet Explorer in the following file
    acl safe_url_for_IE            url_regex -i            "/etc/squid/ACL/safe_url"
    
    ### The ACL for the IE user-agent
    acl internet_explorer browser MSIE
    
    ### The world
    acl all         src 0.0.0.0/0.0.0.0
    
    ### Our network
    acl intranet        src 192.168.1.0/24
    
    ### Let's say we still want a machine in the network to use IE 
    ### (like a guest users not having an alternative web browser installed)
    ### I'm personally allowing IE for the dynamic DHCP clients at work
    acl ie_allowed       src 192.168.1.100/32
    
    ### First, we are allowing the IE-machine here
    http_access allow ie_allowed internet_explorer
    
    ### Secondly, we are denying Internet Explorer, 
    ### except for the "safe URL" list that is still allowed.
    http_access deny internet_explorer !safe_url_for_IE
    
    ### Now, after the restrictions, we are allowing our network.
    http_access allow intranet
    
    ### And finally blocking the rest of the world.
    http_access deny all</code>



The safe URL ACL file (/etc/squid/ACL/safe_url) looks like this :

`http://.*.site1.be/.*
http://.*.site2.be/.*
http://.*.site3.be/.*`

If you have any useful tips or rules, please share ! :)
