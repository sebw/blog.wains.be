---
date: 2006-09-18
title: "OpenLDAP log"
---







categories:
- LDAP
- Linux


OpenLDAP logs via syslogd LOCAL4 so to stream the log you will need to add a line like this to syslog.conf (normally /etc/syslog.conf):

local4.* /var/log/ldap.log

The above command will log all levels of local4 (OpenLDAP) output to the defined file. In many systems you need to create an empty log file before logging will be visible.

The OpenLDAP logging level is set using the following command:

loglevel number

The possible values for number are:
loglevel 	Logging description
-1	enable all debugging
0	no debugging
1	trace function calls
2	debug packet handling
4	heavy trace debugging
8	connection management
16	print out packets sent and received
32	search filter processing
64	configuration file processing
128	access control list processing
256	stats log connections/operations/results
512	stats log entries sent
1024	print communication with shell backends
2048	print entry parsing debugging
