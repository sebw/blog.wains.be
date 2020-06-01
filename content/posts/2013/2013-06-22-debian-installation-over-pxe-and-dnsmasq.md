---
date: 2013-06-22
title: "Debian installation over PXE and dnsmasq"
---







categories:
- Automation
- Debian/Ubuntu


The DHCP/TFTP server holds the IP 10.10.0.2

All commands as root :

mkdir -p /srv/tftp

cd /srv/tftp

wget http://ftp.nl.debian.org/debian/dists/wheezy/main/installer-amd64/current/images/netboot/netboot.tar.gz

tar xvzf netbook.tar.gz

chown dnsmasq. * -R

vim /etc/dnsmasq.conf

dhcp-range=LAN,10.10.20.1,10.10.20.254,255.255.0.0,24h
enable-tftp
tftp-root=/srv/tftp
dhcp-boot=pxelinux.0,pxeserver,10.10.0.2

/etc/init.d/dnsmasq restart
