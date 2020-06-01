---
date: 2006-06-06
title: "Enable IP forwarding under RHEL/CentOS"
---

**The regular way**

Edit /etc/sysctl.conf

Edit the "net.ipv4.ip_forward" line and set it to 1


    
    <code># Kernel sysctl configuration file for Red Hat Linux
    #
    # For binary values, 0 is disabled, 1 is enabled.  See sysctl(8) and
    # sysctl.conf(5) for more details.
    
    # Controls IP packet forwarding
    <strong>net.ipv4.ip_forward = 1</strong>
    
    # Controls source route verification
    net.ipv4.conf.default.rp_filter = 1
    
    # Do not accept source routing
    net.ipv4.conf.default.accept_source_route = 0
    
    # Controls the System Request debugging functionality of the kernel
    kernel.sysrq = 0
    
    # Controls whether core dumps will append the PID to the core filename.
    # Useful for debugging multi-threaded applications.
    kernel.core_uses_pid = 1</code>



**When done type the following to validate the new setting :**

`sysctl -p`

**The manual way :**

`echo "1" > /proc/sys/net/ipv4/ip_forward`

This wouldn't be persistent though, so you should edit sysctl.conf anyway, or add the command in /etc/rc.local


A great guide : [http://www.ducea.com/post/how-to-enable-ip-forwarding-in-linux/](http://www.ducea.com/post/how-to-enable-ip-forwarding-in-linux/)
