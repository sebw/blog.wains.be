---
date: 2006-11-14
title: "Run courier-imap 4 (couriertcpd) as non-root on CentOS/RHEL"
---







categories:
- Howto
- Linux
- Postfix
- Red Hat/CentOS


What follows is an explanation on how I got courier-imap 4 running as non-root on my CentOS 3 system.



I run Postfix 2.2 with virtual domains/users and a mysql backend

Emails are stored here :
drwxr-xr-x    5 postfix  vmail        4.0K Nov 14 00:47 /var/spool/postfix/vmail/

Domain folders are stored under vmail and are owned by vmail.

Emails are delivered to maildir with user vmail.

The issue : courier-imap needs to run as user "vmail" to avoid permissions headaches.


**1. Stop courier-imap service**
service courier-imap stop
service courier-authlib stop


**2. Create a symlink into /etc (just a question of taste)**
ln -s /usr/lib/courier-imap/ /etc/courier-imap


**3. Edit /etc/courier-imap/etc/pop3d**
Uncomment line : TCPDOPTS="-nodnslookup -noidentlookup"
Append this to the line : "-user=vmail"
In order to get this : TCPDOPTS="-nodnslookup -noidentlookup -user=vmail"

pop3d-ssl daemon will automatically inherit options passed at TCPDOPTS in pop3d


**4. Edit /etc/courier-imap/etc/imapd**
Uncomment line : TCPDOPTS="-nodnslookup -noidentlookup"
Append this to the line : "-user=vmail"
In order to get this : TCPDOPTS="-nodnslookup -noidentlookup -user=vmail"

imapd-ssl daemon will automatically inherit options passed at TCPDOPTS in imapd


**5. Create a directory for TLS cache file**
mkdir /var/run/courier-imap
chown vmail:vmail /var/run/courier-imap
chmod 755 /var/run/courier-imap


**6. Edit /etc/courier-imap/etc/pop3d-ssl**
By default, courier-imap will store the SSL cache file under /var/run, change the TLS_CACHEFILE value to this :
TLS_CACHEFILE=/var/run/courier-imap/couriersslcache


**7. Edit /etc/courier-imap/etc/imapd-ssl**
TLS_CACHEFILE=/var/run/courier-imap/couriersslcache


**8. Give rights to authdaemon pid file to user vmail**
chown :vmail /var/spool/authdaemon
chmod 750 /var/spool/authdaemon


**9. Restart courier-imap**
service courier-imap start
service courier-authlib start


Result :

    
    <code>root      9652  0.0  0.0  1516  452 ?        S    19:14   0:00 /usr/sbin/courierlogger -pid=/var/spool/authdaemon/pid -start /usr/ld
    root      9653  0.0  0.0  2416  768 ?        S    19:14   0:00 /usr/libexec/courier-authlib/authdaemond
    root      9654  0.0  0.1  2464  948 ?        S    19:14   0:00 /usr/libexec/courier-authlib/authdaemond
    root      9655  0.0  0.1  2464  948 ?        S    19:14   0:00 /usr/libexec/courier-authlib/authdaemond
    root      9656  0.0  0.1  2464  944 ?        S    19:14   0:00 /usr/libexec/courier-authlib/authdaemond
    root      9657  0.0  0.1  2464  948 ?        S    19:14   0:00 /usr/libexec/courier-authlib/authdaemond
    root      9658  0.0  0.1  2464  948 ?        S    19:14   0:00 /usr/libexec/courier-authlib/authdaemond
    root     10466  0.0  0.0  1520  448 ?        S    19:34   0:00 /usr/sbin/courierlogger...
    vmail    10467  0.0  0.0  2444  676 ?        S    19:34   0:00 /usr/lib/courier-imap/libexec/couriertcpd...
    root     10474  0.0  0.0  1520  448 ?        S    19:34   0:00 /usr/sbin/courierlogger...
    vmail    10475  0.0  0.0  2444  676 ?        S    19:34   0:00 /usr/lib/courier-imap/libexec/couriertcpd...
    root     10481  0.0  0.0  1512  452 ?        S    19:34   0:00 /usr/sbin/courierlogger...
    vmail    10482  0.0  0.0  2448  676 ?        S    19:34   0:00 /usr/lib/courier-imap/libexec/couriertcpd...
    root     10488  0.0  0.0  1512  452 ?        S    19:34   0:00 /usr/sbin/courierlogger...
    vmail    10489  0.0  0.0  2444  676 ?        S    19:34   0:00 /usr/lib/courier-imap/libexec/couriertcpd...
    vmail    13050  0.0  0.2  3436 1572 ?        S    21:15   0:00 /usr/lib/courier-imap/bin/couriertls -server -tcpd /usr/lib/courier-r</code>



You can notice couriertcpd and couriertls instances run as vmail while courierlogger runs as root (which is normal, it is in charge of launching the daemons). authdaemond is running as root as well, this is not an issue, it is in charge of authenticating the user against the MySQL DB.

The sensitive processes here are couriertcpd (for pop3 and imap unsecure) and couriertls (for pop3-ssl and imap-ssl)
