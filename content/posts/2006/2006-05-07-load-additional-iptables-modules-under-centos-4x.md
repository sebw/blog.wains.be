---
date: 2006-05-07
title: "Loading additional iptables modules under CentOS 4.x"
---

If you need a recurrent iptables modules to be loaded (let's say the conntracking modules for FTP connections) you can either :
- issue "modprobe ip_conntrack_ftp" at the CLI everytime you need it
- add "modprobe ip_conntrack_ftp" under rc.local
- edit /etc/init.d/iptables and add "modprobe ip_conntrack" under the "start" argument

or

- the proper way : edit /etc/sysconfig/iptables-config

`IPTABLES_MODULES="ip_conntrack_ftp"`

Anytime you'll start or restart iptables, the modules will be loaded :


    
    <code>[root@localhost](1035)# service iptables condrestart
    Flushing firewall rules:                                   [  OK  ]
    Setting chains to policy ACCEPT: nat filter                [  OK  ]
    Unloading iptables modules:                                [  OK  ]
    Applying iptables firewall rules:                          [  OK  ]
    Loading additional iptables modules: ip_conntrack_ftp      [  OK  ]</code>
