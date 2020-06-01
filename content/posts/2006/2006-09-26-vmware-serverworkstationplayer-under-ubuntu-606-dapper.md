---
date: 2006-09-26
title: "VMware Server/Workstation/Player under Ubuntu 6.06 (Dapper)"
---







categories:
- Linux


I got VMware Workstation running quite easily under Ubuntu 6.06 :

sudo apt-get install linux-headers-`uname -r` build-essential xinetd

unzip the VMW source

cd vmware-distrib

sudo ./vmware-install.pl and follow the steps

Download http://ftp.cvut.cz/vmware/vmware-any-any-update104.tar.gz

Unzip and execute runme.pl

Execute ./vmware-config.pl and follow the configuration steps

You should be able to run /usr/bin/vmware

