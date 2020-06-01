---
date: 2007-02-04
title: "CentOS 4 - chroot DNS with BIND"
---







categories:
- DNS
- Howto
- Linux
- Red Hat/CentOS


I won't go into the details of what is DNS, or the difference between an A record and a CNAME record.. just a quick howto to set up a chrooted DNS server using Bind under CentOS in a mere 5 minutes.. 

Howto available for CentOS 5 : [http://blog.wains.be/post/centos-5-chroot-dns-with-bind/](http://blog.wains.be/post/centos-5-chroot-dns-with-bind/)



**1. Install packages :**

`yum install bind bind-chroot bind-libs bind-utils`

**2. Configure rndc :**

The rndc tool allow to get some useful info on your dns server (stats, status, etc.) so you better get it set up.

rndc.conf is staying out of the chroot cage, for clarity we'll move it into the chroot cage
`cp /etc/rndc.conf /var/named/chroot/etc`

Let's make a symbolic link back in /etc, so we get 3 symlinks pointing to files in the cage (rndc.conf, rndc.key, named.conf) :
`cd /etc
ln -s /var/named/chroot/etc/rndc.conf -f`

Create the rndc key, this command will create your key and store it under /etc/rndc.key :
`rndc-confgen -a`

/etc/rndc.key looks like this :
`key "rndc-key" {
        algorithm       hmac-md5;
        secret "ZK4g84WrfdfsTIQLVs59Eg==";
};`

Pay close attention to the key name, rndc-confgen makes a "rndc-key" while /etc/named.conf calls "rndckey", you'll have to carefully edit your named.conf

Edit /etc/rndc.conf so it looks like this :

`include "/etc/rndc.key";
options {
default-server localhost;
default-key "rndc-key";
};
server localhost {
key "rndc-key";
};`

**3. Set up BIND**

Edit /etc/named.conf so it looks like this :


    
    <code>include "/etc/rndc.key";
    
    // we assume our server has the IP 192.168.100.100 serving the 192.168.100.0/24 subnet
    controls {
            inet 127.0.0.1 allow { 127.0.0.1; } keys { "rndc-key"; };
            inet 192.168.100.100 allow { 192.168.100.0/24; } keys { "rndc-key"; };
    };
    
    options {
            directory "/var/named";
            pid-file "/var/run/named/named.pid";
    
            recursion yes;
    
            allow-recursion {
                    127.0.0.1;
                    192.168.100.0/24;
                    };
    
            // these are the opendns servers (optional)
            forwarders {
                    208.67.222.222;
                    208.67.220.220;
            };
    
            listen-on {
                    127.0.0.1;
                    192.168.100.100;
                    };
    
            /*
             * If there is a firewall between you and nameservers you want
             * to talk to, you might need to uncomment the query-source
             * directive below.  Previous versions of BIND always asked
             * questions using port 53, but BIND 8.1 uses an unprivileged
             * port by default.
             */
            query-source address * port 53;
    
            // so people can't try to guess what version you're running
            version "REFUSED";
    
            allow-query {
                    127.0.0.1;
                    192.168.100.0/24;
                    };
            };
    
    server 192.168.100.100 {
            keys { rndc-key; };
            };
    
    zone "." IN {
            type hint;
            file "named.root";
            };
    
    // we assume we have a slave dns server with the IP 192.168.100.101 
    zone "test.be" IN {
            type master;
            file "data/test.be.zone";
            allow-update { none; };
            allow-transfer { 192.168.100.101; };
            };
    
    };</code>



**4. Your zones**

Download named.root under /var/named/chroot/var/named :
`wget http://www.internic.net/zones/named.root`

Create your first zone under /var/named/chroot/var/named/data/test.be.zone (syntax may not be perfect but is working) :

    
    <code>$ttl 38400
    test.be.       IN      SOA     ns.test.be. admin.test.be. (
                           2007020400   ; Serial
                           10800           ; Refresh after 3 hours
                           3600            ; Retry after 1 hour
                           604800          ; Expire after 1 week
                           86400 )         ; Minimum TTL of 1 day
    test.be.       IN      NS      ns.test.be.
    
    test.be.               IN      MX      1       mx.test.be.
    test.be.               IN      MX      5       mx2.test.be.
    
    ns.test.be.           IN      A       192.168.100.10
    mx.test.be.          IN      A       192.168.100.20
    mx2.test.be.         IN      A       192.168.100.21
    mail.test.be.          IN      CNAME   mx.test.be.</code>



**5. Set up your server to query its own dns server**

Edit /etc/resolv.conf and add this line at the top of the file :
`nameserver 127.0.0.1`

**6. Start the service**

Start the DNS server : 
`service named start`

Make sure the service will start at reboot : 
`chkconfig named on`

**7. Query !**

Now, you should be able to send your first query to the server : 
`$ host mx.test.be
mx.test.be has address 192.168.100.20`

**8. More...**

You can install the package "caching-nameserver" along with the base packages.
This is what is provided by the package :
`$ rpm -ql caching-nameserver
/etc/named.conf
/usr/share/doc/caching-nameserver-7.3
/usr/share/doc/caching-nameserver-7.3/Copyright
/usr/share/doc/caching-nameserver-7.3/rfc1912.txt
/var/named/localdomain.zone
/var/named/localhost.zone
/var/named/named.broadcast
/var/named/named.ca
/var/named/named.ip6.local
/var/named/named.local
/var/named/named.zero`

With that, you don't have to bother creating your localhost zone or retrieve the named.root file from internic (named here named.ca).

Also, your /etc/named.conf file is already populated with some data

More info : [http://www.section6.net/wiki/index.php/Using_DNS_with_BIND](http://www.section6.net/wiki/index.php/Using_DNS_with_BIND)
