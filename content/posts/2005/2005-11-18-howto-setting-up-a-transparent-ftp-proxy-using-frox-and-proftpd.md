---
date: 2005-11-18
title: "How to set up a transparent FTP proxy using frox"
---

Running a transparent FTP proxy is an easy way to control FTP connections made by people on your network (using ACL's)
If you are already running Squid as a transparent (web) proxy, it cannot act as a transparent FTP proxy along, thus you have to use another tool for FTP proxying : frox will do the job



**Installation & compilation**

Grab the latest version of Frox at [http://frox.sourceforge.net/](http://frox.sourceforge.net/)
Compile the package the usual way..

The following files should be installed :

/etc/frox.conf
/usr/local/sbin/frox
/var/log/frox/frox-log
/var/run/frox.pid

**/etc/frox.conf :**
`Listen 192.168.0.1
Port 2121
BindToDevice eth1 < -- depends on your config, should be the LAN NIC
ResolvLoadHack wontresolve.doesntexist.abc
User nobody
Group nobody
WorkingDir /usr/local/bin
DontChroot Yes
LogLevel 20
LogFile /var/log/frox/frox-log
XferLogging yes
PidFile /var/run/frox.pid
BounceDefend yes
PassivePorts 49152-65534
MaxForks 10
MaxForksPerHost 4
### Allow rules first, deny rules next
ACL Allow 192.168.0.2/255.255.255.255 - * 21 <-- this will allow 192.168.0.2 to access ANY FTP server (internal AND external)
ACL Allow 192.168.0.3/255.255.255.255 - 193.190.198.20 21 <-- this will allow 192.168.0.3 to access ftp.belnet.be server
ACL Allow 192.168.0.4/255.255.255.255 - 192.168.0.1 21 <-- this will allow 192.168.0.4 to access the internal server
ACL Deny 192.168.254.0/255.255.255.0 - * 21 <-- this will block anything else from the subdomain`

**Red Hat/Fedora/CentOS init script**

I made a pretty short init script to start frox as a service on Red Hat based machines

Save the following script under **/etc/init.d/frox :**
``### /etc/init.d/frox ###
#!/bin/bash
#
#
FROX_BIN=/usr/local/sbin/frox
FROX_CONF=/etc/frox.conf
FROX_LOG=/var/log/frox/frox-log
FROX_PID=/var/run/frox.pid
case "$1" in
'start')
echo "Starting Frox...";
$FROX_BIN -f $FROX_CONF
;;
'stop')
echo "Stopping Frox...";
if [ -f $FROX_PID ]; then
kill `cat $FROX_PID`
rm $FROX_PID
else
echo "Frox not running";
fi
;;
'help')
echo "Usage: $0 { start | stop }"
exit 1
;;
esac
exit 0
### EOF ###`

Type :
`chkconfig --add /etc/init.d/frox
service frox start`

Frox should start

**Iptables configuration**

Add the following line to /etc/sysconfig/iptables under NAT section
Anyone under 192.168.0.0/24 trying to access port 21 will be transparently redirected to frox, which will allow or deny the connection
`-A PREROUTING -s 192.168.0.0/24 -p tcp -m tcp --dport 21 -j REDIRECT --to-ports 2121`

Type : `service iptables restart`

Test your configuration

Telnet into your frox server and check out the logs using :
`tail -f /var/log/frox/frox-log`

If you want to lock down iptables, you'll run into problems : see [http://blog.wains.be/?p=81](http://blog.wains.be/?p=81)
