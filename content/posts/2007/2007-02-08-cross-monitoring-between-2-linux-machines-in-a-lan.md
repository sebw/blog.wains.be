---
date: 2007-02-08
title: "Cross-monitoring 2 Linux machines in a LAN"
---







categories:
- Howto
- Linux
- Security


**Scenario :**

You want 2 Linux machines in your network to monitor themselves.
If one machines goes down, the other sends an email to the admin.



machine A has the IP 192.168.1.1 (machineA.local.domain.be)
machine B has the IP 192.168.1.2 (machineB.local.domain.be)

**Configuration :**

Install heartbeat.
CentOS/RHEL Packages available here : [http://dev.centos.org/centos/4/testing/i386/RPMS/](http://dev.centos.org/centos/4/testing/i386/RPMS/)

**/etc/ha.d/authkeys**
Machine A + B :
`auth 3
3 md5 thisisarandomstringofcharacters`

The authkey is a shared secret between the machines, thus it has to be the same on the two machines.

**/etc/ha.d/haresources**
Machine A :
`machineB.local.domain.be MailTo::admin@domain.be::HEARTBEAT_ALERT`

Machine B :
`machineA.local.domain.be MailTo::admin@domain.be::HEARTBEAT_ALERT`

haresources defines the action if a machine goes down.
If machine(A|B) notices machine(B|A) is down, it'll use the "MailTo" resource (in /etc/ha.d/resource.d/) to send a warning email to admin@domain.be

**/etc/ha.d/ha.cf**
Machine A :
`logfacility     local0
keepalive 5
deadtime 15
warntime 10
initdead 120
udpport 694
ucast eth0 192.168.1.2
auto_failback on
node    machineB.local.domain.be
node    machineA.local.domain.be`

Machine B :
`logfacility     local0
keepalive 5
deadtime 15
warntime 10
initdead 120
udpport 694
ucast eth0 192.168.1.1
auto_failback on
node    machineB.local.domain.be
node    machineA.local.domain.be`

Logfacility : log messages will be sent to /var/log/messages
keepalive : 5 seconds between checks
deadtime : how long before it declares the other host down
warntime : how long it takes before sending the warning
ucast : if cross-monitoring machines, use unicast which is less disruptive, else, use "bcast ethX"
node : specify the nodes to monitor
