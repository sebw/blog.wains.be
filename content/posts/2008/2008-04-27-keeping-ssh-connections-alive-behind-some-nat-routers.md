---
date: 2008-04-27
title: "Keeping SSH connections alive behind some NAT routers"
---







categories:
- SSH


SSH connections made from behind my Linksys WAG54G NAT gateway like to die after idling for something like 5 minutes.

**The fix :**

Add in **/home/USER/.ssh/config**


    
    <code>Host *
       ServerAliveInterval 60
       ServerAliveCountMax 60</code>



The SSH client will send a packet every 60 seconds in order to keep the connection alive.
The second option means that after 60 keepalive packets sent, it will stop trying to keep the connection alive, and the connection will eventually die.

**See what OpenSSH FAQ has to say about it :**

_2.12 - My ssh connection freezes or drops out after N minutes of inactivity.

This is usually the result of a packet filter or NAT device timing out your TCP connection due to inactivity. You can enable ClientAliveInterval in the server's sshd_config, or enable ServerAliveInterval in the client's ssh_config (the latter is available in OpenSSH 3.8 and newer).

Enabling either option and setting the interval for less than the time it takes to time out your session will ensure that the connection is kept "fresh" in the device's connection table. _

**Link :**

[http://www.openssh.com/faq.html](http://www.openssh.com/faq.html)

