---
date: 2007-12-30
title: "CentOS 5 - preventing brute force attacks with iptables"
---







categories:
- Iptables
- Linux
- Red Hat/CentOS
- Security


Based on [http://e18.physik.tu-muenchen.de/~tnagel/ipt_recent/](http://e18.physik.tu-muenchen.de/~tnagel/ipt_recent/)

The following example is much simpler, it blocks hosts trying to connect more than 3 times to the SSH server within 60 seconds. If you need something more complex, check out the howto mentionned above.

This is my /etc/sysconfig/iptables


    
    <code>*filter
    :INPUT ACCEPT [0:0]
    :FORWARD ACCEPT [0:0]
    :OUTPUT ACCEPT [0:0]
    :LIMIT_SSH - [0:0]
    
    # accept localhost and related/established traffic
    -A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT 
    -A INPUT -i lo -j ACCEPT 
    
    # transfer connections made to tcp/22 to the LIMIT_SSH chain
    -A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -j LIMIT_SSH 
    
    # block anything else in the INPUT chain
    -A INPUT -j DROP 
    
    # if host has made more than 3 attempts in 60 seconds, drop it
    -A LIMIT_SSH -m recent --set --name SSH
    -A LIMIT_SSH -m recent --update --seconds 60 --hitcount 4 --name SSH -j DROP 
    -A LIMIT_SSH -j ACCEPT 
    
    COMMIT</code>




Recent module homepage : [http://www.snowman.net/projects/ipt_recent/](http://www.snowman.net/projects/ipt_recent/)

