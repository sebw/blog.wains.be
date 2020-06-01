---
date: 2007-09-27
title: "Using lsof to get network information"
---







categories:
- Howto
- Linux
- Networks


I often use netstat to get network information ("netstat -napee" producing my favorite verbose output)

You can also use lsof, which primary use is to list open files.
Ideally, you will run lsof as root to get network info from privileged ports (< 1024).


The syntax is :
`# lsof -i[46][protocol][@hostname|hostaddr][:service|port]`


List any network info (as with many other commands, -n avoids name resolution, which makes lsof faster) :

    
    <code># lsof -i -n
    COMMAND    PID     USER   FD   TYPE DEVICE SIZE NODE NAME
    sshd      4808     root    3u  IPv6  15853       TCP *:ssh (LISTEN)
    cupsd     4884     root    2u  IPv4  16190       TCP 127.0.0.1:ipp (LISTEN)
    mysqld    4960    mysql   13u  IPv4  16267       TCP 127.0.0.1:mysql (LISTEN)
    avahi-dae 5187    avahi   14u  IPv4  16589       UDP *:mdns 
    avahi-dae 5187    avahi   15u  IPv4  16590       UDP *:32768 
    apache2   5387     root    3u  IPv6  16957       TCP *:www (LISTEN)
    apache2   5485 www-data    3u  IPv6  16957       TCP *:www (LISTEN)
    apache2   5486 www-data    3u  IPv6  16957       TCP *:www (LISTEN)
    apache2   5487 www-data    3u  IPv6  16957       TCP *:www (LISTEN)
    apache2   5488 www-data    3u  IPv6  16957       TCP *:www (LISTEN)
    apache2   5489 www-data    3u  IPv6  16957       TCP *:www (LISTEN)
    dhclient  5759     dhcp    6u  IPv4  19387       UDP *:bootpc 
    pidgin    5964       sw   18u  IPv4  23156       TCP 192.168.1.2:42225->209.85.163.125:xmpp-client (ESTABLISHED)</code>




Listing network info from port 22 (sshd) :

    
    <code># lsof -i TCP:22 -n
    COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
    sshd    4808 root    3u  IPv6  15853       TCP *:ssh (LISTEN)</code>




Listing network info from a specified host :

    
    <code># lsof -i TCP@209.85.163.125 -n
    COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
    pidgin  5964   sw   18u  IPv4  23156       TCP 192.168.1.2:42225->209.85.163.125:xmpp-client (ESTABLISHED)</code>




If you were to list any protocol activity related to that host, you would use this syntax :

    
    <code># lsof -i @209.85.163.125 -n
    COMMAND  PID USER   FD   TYPE DEVICE SIZE NODE NAME
    pidgin  5964   sw   18u  IPv4  23156       TCP 192.168.1.2:42225->209.85.163.125:xmpp-client (ESTABLISHED)</code>




You can even combine commands to list the network activity from a particular user using a particular program :

    
    <code># lsof -a -u sw -c firefox-bin -i -n
    COMMAND    PID USER   FD   TYPE DEVICE SIZE NODE NAME
    firefox-b 5839   sw   42u  IPv4  43265       TCP 192.168.1.2:42474->66.249.91.83:https (ESTABLISHED)
    firefox-b 5839   sw   45u  IPv4  43034       TCP 192.168.1.2:41888->66.102.11.164:www (ESTABLISHED)
    firefox-b 5839   sw   50u  IPv4  43042       TCP 192.168.1.2:56649->208.69.34.230:www (ESTABLISHED)
    firefox-b 5839   sw   55u  IPv4  43111       TCP 192.168.1.2:58894->72.14.255.99:www (ESTABLISHED)
    firefox-b 5839   sw   58u  IPv4  43054       TCP 192.168.1.2:55300->66.150.96.119:www (ESTABLISHED)</code>



According to the manpages :
-a       This option causes list selection options to be ANDed..
-u       This option selects the listing of files for the user...
-c       This option selects the listing of files for processes executing the command specified..


In conclusion, in a multi-user environment, lsof is a better alternative than netstat..
If you have other good recipes, drop a comment...
