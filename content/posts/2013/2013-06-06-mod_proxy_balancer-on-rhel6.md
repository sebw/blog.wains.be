---
date: 2013-06-06
title: "mod_proxy_balancer on RHEL6"
---







categories:
tags: Apache
tags: Linux
tags: Red Hat/CentOS


Tested on RHEL 6. This is the simplest setup possible, for my own reference. I may come up with a [Salt](http://saltstack.com/community.html) state in the future.

Reference :[ http://httpd.apache.org/docs/2.2/mod/mod_proxy_balancer.html](http://httpd.apache.org/docs/2.2/mod/mod_proxy_balancer.html)

/etc/httpd/conf.d/balancer-manager.conf :
`




<Location /balancer-manager>




SetHandler balancer-manager







Order Deny,Allow




Deny from all




Allow from 192.168.0.0/24




</Location>

`








/etc/httpd/conf.d/vhost.conf :





`

<VirtualHost *:80>




        ServerAdmin someadmin@example.org







        ServerName xyz.example.org[
](http://xyz.etnic.be/)







        <Proxy balancer://xyz_example_org>




                BalancerMember http://backend01.example.org:80




                BalancerMember http://backend02.example.org:80




        </Proxy>




        ProxyPass /balancer-manager !




        ProxyPass / balancer://xyz_example_org







</VirtualHost>

`





Access the cluster through http://xyz.example.org




Manager the cluster through http://xyz.example.org/balancer-manager (as we prevent the path from being 'reverse proxied')



