---
date: 2007-12-13
title: "CentOS 5 - chroot DNS with bind"
---







categories:
- DNS
- Howto
- Linux
- Red Hat/CentOS


Howto for CentOS 4 here : [http://blog.wains.be/post/centos-chroot-dns-with-bind/](http://blog.wains.be/post/centos-chroot-dns-with-bind/)


**1. Install packages :**

`yum install bind bind-chroot bind-libs bind-utils caching-nameserver`


**2. Configure RNDC :**

`cd /var/named/chroot/etc
rndc-confgen > rndc.key
chown root:named rndc.key`

Edit rndc.key so it looks like this :

`key "rndckey" {
        algorithm hmac-md5;
        secret "SGsvd1dF+mv+yU4ywCCkkg==";
};`

You DON'T NEED anything else in the file **(you must remove some option lines !)**


A symlink in /etc exists and points to the rndc.key file we've just created, named expects that file there in order to be able to authenticate against rndc.


**3. Configure /var/named/chroot/etc/named.conf**


    
    <code>// we include the rndckey (copy-paste from rndc.key created earlier)
    key "rndckey" {
          algorithm hmac-md5;
          secret "SGsvd1dF+mv+yU4ywCCkkg==";
    };
    
    // we assume our server has the IP 192.168.254.207 serving the 192.168.254.0/24 subnet
    controls {
            inet 127.0.0.1 allow { 127.0.0.1; } keys { "rndckey"; };
            inet 192.168.254.207 allow { 192.168.254.0/24; } keys { "rndckey"; };
    };
    
    options {
            directory "/var/named";
            pid-file "/var/run/named/named.pid";
    
            recursion yes;
    
            allow-recursion {
                    127.0.0.1;
                    192.168.254.0/24;
                    };
    
            // these are the opendns servers (optional)
            forwarders {
                    208.67.222.222;
                    208.67.220.220;
            };
    
            listen-on {
                    127.0.0.1;
                    192.168.254.207;
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
                    192.168.254.0/24;
                    };
            };
    
    server 192.168.254.207 {
            keys { rndckey; };
            };
    
    zone "." IN {
            type hint;
            file "named.ca";
            };
    
    // forward zone
    zone "test.be" IN {
            type master;
            file "data/test.be.zone";
            allow-update { none; };
            // we assume we have a slave dns server with the IP 192.168.254.101
            allow-transfer { 192.168.254.101; };
            };
    
    // reverse zone
    zone "250.168.192.in-addr.arpa" IN {
    	type master;
    	file "data/192.168.250.zone";
    	allow-update { none; };
            allow-transfer { 192.168.254.101; };
    	};</code>




**4. Our first zone**

Let's say I own the domain test.be

We create our first zone under /var/named/chroot/var/named/data/test.be.zone

Here's an example :


    
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
    
    www.test.be.           IN      A       192.168.100.5
    ns.test.be.           IN      A       192.168.100.10
    mx.test.be.          IN      A       192.168.100.20
    mx2.test.be.         IN      A       192.168.100.21
    mail.test.be.          IN      CNAME   mx.test.be.</code>



Here's the corresponding reverse zone under /var/named/chroot/var/named/data/192.168.100.zone :


    
    <code>$TTL 86400
    100.168.192.in-addr.arpa.	IN	SOA	ns.test.be. admin.test.be. (
    			2007032000
    			10800
    			900
    			604800
    			3600 )
    
    100.168.192.in-addr.arpa.	IN	NS	ns.test.be.
    
    20.100.168.192.in-addr.arpa. IN PTR mx.test.be.
    5.100.168.192.in-addr.arpa. IN PTR www.test.be.</code>




**5. Start the service and make sure it'll start at boot**

`service named start
chkconfig named on`

Make sure it's running :
`# rndc status
number of zones: 1
debug level: 0
xfers running: 0
xfers deferred: 0
soa queries in progress: 0
query logging is OFF
recursive clients: 0/1000
tcp clients: 0/100
server is up and running`


**6. Query**


    
    <code># nslookup mx.test.be. 127.0.0.1
    Server:         127.0.0.1
    Address:        127.0.0.1#53
    
    Name:   mx.test.be
    Address: 192.168.100.20
    
    
    # nslookup www.google.com. 127.0.0.1
    Server:         127.0.0.1
    Address:        127.0.0.1#53
    
    Non-authoritative answer:
    www.google.com  canonical name = www.l.google.com.
    Name:   www.l.google.com
    Address: 216.239.59.99
    Name:   www.l.google.com
    Address: 216.239.59.103
    Name:   www.l.google.com
    Address: 216.239.59.104
    Name:   www.l.google.com
    Address: 216.239.59.147</code>




**7. /etc/resolv.conf**

If the query made on the previous point is working, you can set up /etc/resolv.conf on the server.

It should look like this :
`search test.be
nameserver 127.0.0.1`

