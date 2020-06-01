---
date: 2005-12-14
title: "Script - check services status easily"
---

Since arpwatch and spamassassin crashed for some unknown reasons (probably bugs) lately, I needed a tool that would monitor services status on a regular basis.

I found Nagios [http://www.nagios.org/](http://www.nagios.org/) but it was not really what I needed. Indeed, Nagios can only monitor some specific services (usually services opening a tcp port) and it offered just way too many features, thus requiring a lot of dependencies and was not the easiest piece of software to install.

To do the job, I made this (very) little script that runs from a cron on a daily basis :




    
    <code>#!/bin/sh
    
    SERVICES_REGULAR="squid smb 3dm mysqld postfix frox dhcpd sshd spamassassin httpd courier-imap arpwatch xinetd"
    
    echo "Services"
    echo "========"
    for services_regular in $SERVICES_REGULAR
    do
    /sbin/service $services_regular status | sed -re 's/(pid|(|)|[0-9]{2,}|...| )//g;s/is/ : /g'
    done
    /sbin/service named status | grep server | sed -re 's/server/named/g;s/is/:/g'
    echo " "</code>



The output is :

Services
========
squid : running
smbd : running
nmbd : running
3dmd : running
mysqld : running
master : running
Frox : running
dhcpd : running
sshd : running
spamd : running
httpd : running
arpwatch : running
xinetd : running
named : up and running

This should work on many Red Hat flavors, if you take a look at the code, named doesn't output a regular "service is (running|stopped)", I needed to make a special line for this service
