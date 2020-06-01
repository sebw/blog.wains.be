---
date: 2009-09-24
title: "Script - renew your IP automatically through your router web interface"
---







categories:
- Scripts


**A little background :**

If you are not interested in the explanation behind this, skip to the script section.

OK, I've been using Scarlet ISP for my DSL connection for something like 5 years. 
My contract mentions I get a dynamic IP.
The thing is, until two days ago, as long as my DSL router remained connected, I was keeping the same IP.
Scarlet didn't mind until Belgacom ISP bought them.

If you are a residential customer, Belgacom do not want you to keep the same IP for extended period of time (and by extended, Belgacom mean "2 days")... Unless you pay for the professional package, of course.

So Belgacom disconnect residential customers on purpose every 36 hours. You'll note they don't disconnect every 24 hours. Smart alec could disconnect and reconnect their modem at 4am and not be distracted by disconnections ever again.

With the 36 hour rule, whatever you do, you'll be disconnected in the middle of the day once every two days, say goodbye to your SSH sessions, rsync backups, etc..

In order to avoid such problems, I came up with a little script that I run every night at 1am.

Basically, it goes through the web interface of my Linksys WAG200G, click the disconnect button, wait for 5 seconds and click the connect button.

The "challenge" was to figure out which variables I needed to pass in the POST request, for that I used the firefox addon "[Tamper data](https://addons.mozilla.org/en-US/firefox/addon/966)".


**The script :**


    
    <code>#!/usr/bin/perl -w
    
    use strict;
    use warnings;
    use LWP;
    use HTTP::Request::Common;
    
    our $ua = LWP::UserAgent->new;
    
    $ua->request(POST 'http://LOGIN:PASSWORD@192.168.177.254/apply.cgi',
    [submit_button => 'Status_Router', submit_type => 'disconnect',
    change_action => 'gozila_cgi', wan_proto => 'dhcp', status_connection => '0']);
    
    sleep 5;
    
    $ua->request(POST 'http://LOGIN:PASSWORD@192.168.177.254/apply.cgi',
    [submit_button => 'Status_Router', submit_type => 'connect',
    change_action => 'gozila_cgi', wan_proto => 'dhcp', status_connection => '0']);</code>




My dad's router (SMC 7908) was a bit trickier as it required to pass the complete header set and referer or it rejected the authentication, by the way I decided to give curl a try :


    
    <code>#!/bin/sh
    
    # First we need to auth with headers and referer.. Authentication is IP based so after auth we can access the router config from any browser
    /usr/bin/curl -d 'pws=password' -H "Host:192.168.2.1" -A "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.1.3) Gecko/20090824 Firefox/3.5.3" -H "Accept:text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8" -H "Accept-Language:en-us,en;q=0.5" -H "Accept-Encoding:gzip,deflate" -H "Accept-Charset:ISO-8859-1,utf-8;q=0.7,*;q=0.7" -H "Keep-Alive:300" -H "Connection:keep-alive" -e "http://192.168.2.1/login.stm" http://192.168.2.1/cgi-bin/login.exe
    
    sleep 3
    
    # Now we can proceed, we only need to disconnect, the router reconnects automatically
    /usr/bin/curl -d 'pvc=0' -d 'cur_if=11' -d 'disconnect=Disconnect' -H "Host:192.168.2.1" -A "Mozilla/5.0 (Macintosh; U; Intel Mac OS X 10.6; en-US; rv:1.9.1.3) Gecko/20090824 Firefox/3.5.3" -H "Accept:text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8" -H "Accept-Language:en-us,en;q=0.5" -H "Accept-Encoding:gzip,deflate" -H "Accept-Charset:ISO-8859-1,utf-8;q=0.7,*;q=0.7" -H "Keep-Alive:300" -H "Connection:keep-alive" -e "http://192.168.2.1/login.stm" http://192.168.2.1/cgi-bin/statusprocess.exe</code>





This is a very interesting link with reconnect scripts for a lot of routers : [http://www.paehl.de/reconnect/Curl_search.php?router=firmware](http://www.paehl.de/reconnect/Curl_search.php?router=firmware)


