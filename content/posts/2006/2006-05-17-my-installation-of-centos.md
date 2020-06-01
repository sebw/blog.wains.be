---
date: 2006-05-17
title: "My installation of CentOS"
---

Please pay attention this is my personal setup for a SERVER (running only CLI etc)



**Edit /etc/sysconfig/network and add (disable zeroconf, allow routing) :**
`NOZEROCONF=yes
FORWARD_IPV4=yes`

Disable ipv6 :
`echo "alias net-pf-10 off” >> /etc/modprobe.conf`

**"yum erase" these:**
`acpid
apmd
anacron
at
atd
bluez-bluefw
bluez-hcidump
bluez-libs
bluez-utils
cups
dhclient
dhcpv6_client
dmraid
eject
finger
hal
htmlview
irda-utils
isdn4k-utils
lftp
mailx
mgetty
minicom
ncurses
NetworkManager
nfs-utils
openldap
pcmcia-cs
pinfo
pm -e irda-utils
portmap
ppp
procmail
proftpd
python-sqlite
redhat-logos
redhat-lsb
redhat-menus
rhnlib
rp-pppoe
rsh
setools
setserial
setuptool
sqlite
system-config-securitylevel-tui
up2date
wireless-tools
wvdial
xinetd
ypbind yp-tools`

Then, **yum update**

**Configure tcp_wrapper :**
`/etc/hosts.allow
/etc/hosts.deny
/etc/hosts.sshd`

**Properly configure SSH :**
`port xx22 < -- run on a different port, security through obscurity
private key only`

**Configure Grub :**
``Set a md5 password`

**Replace sendmail by postfix**
`yum install system-switch-mail postfix
$ system-switch-mail
yum erase sendmail`

**Edit /etc/inittab :**
`Comment mingetty tty(3|4|5|6)
Comment ctrl alt del`
Reload inittab : `init q`

**Configure Iptables**

**Suppress services / stop via chkconfig**

**rpm -e kernel-utils if kernel 2.6.x**

**Services not needed : acpi nfs kudzu apmd xinetd**

**Make yum aliases**

This is not meant to be a guide that would fit to your configuration, it fits to mine
