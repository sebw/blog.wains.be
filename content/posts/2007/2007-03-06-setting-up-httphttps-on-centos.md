---
date: 2007-03-06
title: "Setting up http/https on CentOS"
---







categories:
- RHCE


Packages needed : httpd + deps
Package needed for https : mod_ssl

yum install httpd mod_ssl

service httpd start
chkconfig httpd on

Done !

I'm not sure what they could ask about Apache at the RHCE exam.. ? Virtual domains ?
