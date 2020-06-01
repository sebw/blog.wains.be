---
date: 2016-06-18
title: "Securing Elasticsearch API with Haproxy"
---

2016-06-18

Elasticsearch API is fully open by default and anyone can create (PUT method) or delete indices (DELETE method).

In a project at work, I was asked to install a fully redondant cluster, and among other things expose the API:  
- over HTTP with no auth for GET method for developers   
- over HTTPS with auth for any methods (PUT, DELETE, etc.) for Elastic admins

The Elastic cluster is orchestrated with Saltstack (I'll publish the states later) and is made of 8 nodes, two of them act as clients (no master nor data role) and are only accessible from two HAproxy nodes (secured with iptables). 

This is the HAproxy configuration, tested on RHEL7.

I intentionally used insecure-password for authentication over HTTPS, so you can basically copy and paste this configuration and test. Please use "password" when you're ready to go in production.

	global        log graylog.example.org:10514    local0        log-send-hostname        chroot /var/lib/haproxy        user haproxy        group haproxy        daemon        maxconn 4000	defaults        log     global        mode    http        option  httplog        option  dontlognull        option redispatch        retries 3        timeout client 35s        timeout server 60s        timeout connect 5s        timeout http-keep-alive 10s	listen stats :8080		stats enable   		stats uri /		stats realm Haproxy\ Statistics		stats auth admin:john_snuuuh	backend elastic-backend-http-9200        mode http        timeout connect 10s        timeout server 30s        balance roundrobin        server selk01logger01prd-9200 selk01logger01prd.intranet.example.org:9200 weight 1 maxconn 512 check        server selk01logger02prd-9200 selk01logger02prd.intranet.example.org:9200 weight 1 maxconn 512 check	frontend elastic-frontend-http-80        bind *:80         mode http        option forwardfor        option http-server-close        option http-pretend-keepalive		acl ELASTIC_GET method GET		use_backend elastic-backend-http-9200 if ELASTIC_GET	userlist ELASTIC_ADMIN		user elasticadmin insecure-password ThisShouldBeChanged	frontend elastic-frontend-https-443        bind *:443 ssl crt /etc/ssl/certs/mycert.pem        mode http        option forwardfor        option http-server-close        option http-pretend-keepalive		acl AUTH_E2SA http_auth(ELASTIC_ADMIN)		http-request allow if AUTH_E2SA		http-request deny        default_backend elastic-backend-http-9200
        
