---
date: 2006-02-11
title: "MySQL issue after upgrading from Courier-Imap 3 to 4"
---

Along with CentOS 4.2 came MySQL 4.1, great news ! BUT...

My old Courier-Imap version was only compatible with MySQL 3.x..

I had to upgrade to Courier-imap 4. My buddy kindly compiled the packages for me and published it on his repository : [http://repo.securityteam.us/repository/](http://repo.securityteam.us/repository/)

A few issue then came up with my imap configuration, I noticed a few changes had been made to Courier-Imap about MySQL connectivity..

As explained here  [http://blog.wains.be/?p=7](http://blog.wains.be/?p=7), mysql configuration files of courier used to be under /usr/lib/courier-imap/etc..

The new version of courier requires the files to be located under /etc/authlib or courier will just complain about mysql.example.com not being reachable :

`authdaemond: failed to connect to mysql server (server=mysql.example.com, userid=admin): Unknown MySQL serv)`

You just need to move your configuration files into the new directory and you are done.. 
It took me 20 minutes to figure out the relocation of the files :)

More info : [http://www.courier-mta.org/authlib/](http://www.courier-mta.org/authlib/)
