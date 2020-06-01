---
date: 2006-07-27
title: "Installing Modsecurity"
---

From http://michael-and-mary.net/?q=node/1261

Modsecurity is an open source web application firewall for the Apache server that helps to keep the malicious people at bay. To install Modsecurity, first install the package: httpd-devel (yum install httpd-devel) [as root]. Next, execute the following commands (as root) to install and configure Modsecurity:



cd /root

wget http://www.modsecurity.org/download/modsecurity-apache_1.9.4.tar.gz

tar -xvzf modsecurity-apache_1.9.4.tar.gz

cd modsecurity-apache_1.9.4/apache2/

/usr/sbin/apxs -cia mod_security.c

The last line of the compilation should read: activating module 'security' in /etc/httpd/conf/httpd.conf, indcating that modsecurity is successfully built and installed.

Delete the downloaded package by executing the commands [as root]:

cd /root

rm -rf modsecurity*

Next, get the Modsecurity rulesets and install them. Execute the following commands to fetch and install the rulesets [as root]:

cd /etc/httpd/conf.d/

wget http://www.modsecurity.org/download/rules/modsecurity-general.conf

wget http://www.modsecurity.org/download/rules/modsecurity-php.conf

wget http://www.modsecurity.org/download/rules/modsecurity-output.conf

wget http://www.gotroot.com/downloads/ftp/mod_security/apache2/apache2-gotrootrules-latest.tar.gz

tar -xvzf apache2-gotrootrules-latest.tar.gz

mv apache2/* .

rm -rf apache2/

rm -rf apache2-gotrootrules-latest.tar.gz

Next, restart Apache to read the new rules. Execute the command: /sbin/service httpd restart (as root) to restart Apache.

The rules are updated very frequently, so keep updating them to get the latest protection. 
