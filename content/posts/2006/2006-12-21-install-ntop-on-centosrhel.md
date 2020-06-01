---
date: 2006-12-21
title: "Install ntop on CentOS/RHEL"
---







categories:
- Howto
- Linux


What is ntop ? : [http://www.ntop.org/overview.html](http://www.ntop.org/overview.html)

Install the SecurityTeamUS repo :
`rpm -ihv http://repo.securityteam.us/repository/redhat/securityteamus-repo-latest.rpm`

**Install ntop (dependencies (gd) included on the base repo) :**
`yum install ntop`



ntop requires that you create a password for it to run, type ntop at the prompt : 
`Thu Dec 21 19:53:54 2006  NOTE: Interface merge enabled by default
Thu Dec 21 19:53:54 2006  Initializing gdbm databases
Thu Dec 21 19:53:54 2006  ntop will be started as user nobody
Thu Dec 21 19:53:54 2006  ntop v.3.2 (http://repo.securityteam.us/repository/>Provided by SecurityTeamUS)
Thu Dec 21 19:53:54 2006  Configured on Dec 21 2006 11:58:58, built on Dec 21 2006 12:03:59.
Thu Dec 21 19:53:54 2006  Copyright 1998-2005 by Luca Deri 
Thu Dec 21 19:53:54 2006  Get the freshest ntop from http://www.ntop.org/
Thu Dec 21 19:53:54 2006  NOTE: ntop is running from 'ntop'
Thu Dec 21 19:53:54 2006  NOTE: (but see warning on man page for the --instance parameter)
Thu Dec 21 19:53:54 2006  NOTE: ntop libraries are in '/usr/lib'
Thu Dec 21 19:53:54 2006  Initializing ntop
Thu Dec 21 19:53:54 2006  Checking eth0 for additional devices
Thu Dec 21 19:53:54 2006  Resetting traffic statistics for device eth0
Thu Dec 21 19:53:54 2006  DLT: Device 0 [eth0] is 1, mtu 1514, header 14
Thu Dec 21 19:53:54 2006  Initializing gdbm databases
Thu Dec 21 19:53:54 2006  VENDOR: Loading MAC address table.
Thu Dec 21 19:53:54 2006  VENDOR: Checking for MAC address table file
Thu Dec 21 19:53:54 2006  VENDOR: File '/etc/ntop/specialMAC.txt.gz' does not need to be reloaded
Thu Dec 21 19:53:54 2006  VENDOR: ntop continues ok
Thu Dec 21 19:53:54 2006  VENDOR: Checking for MAC address table file
Thu Dec 21 19:53:54 2006  VENDOR: File '/etc/ntop/oui.txt.gz' does not need to be reloaded
Thu Dec 21 19:53:54 2006  VENDOR: ntop continues ok
Thu Dec 21 19:53:54 2006  Fingeprint: Loading signature file.
Thu Dec 21 19:53:54 2006  Fingeprint: ...loaded 1697 records
Thu Dec 21 19:53:54 2006  ASN: Checking for Autonomous System Number table file
Thu Dec 21 19:53:54 2006  **WARNING** ASN: Unable to open file 'AS-list.txt'
Thu 21 Dec 2006 07:53:54 PM CET  I18N: Default language (from ntop host) is 'en_US'
Thu 21 Dec 2006 07:53:54 PM CET  I18N: This instance of ntop supports 0 additional language(s)
Thu 21 Dec 2006 07:53:54 PM CET  IP2CC: Checking for IP address  Country Code mapping file
Thu 21 Dec 2006 07:53:54 PM CET  IP2CC: Loading file '/etc/ntop/p2c.opt.table.gz'
Thu 21 Dec 2006 07:53:54 PM CET  IP2CC: ...found 52395 lines
Thu 21 Dec 2006 07:53:54 PM CET  GDVERCHK: Guessing at libgd version
Thu 21 Dec 2006 07:53:54 PM CET  **WARNING** GDVERCHK: Unable to load gd, message is 'libgd.so: cannot open shared object file: No such file or directory'
Thu 21 Dec 2006 07:53:54 PM CET  GDVERCHK: ... as 2.x
Thu 21 Dec 2006 07:53:54 PM CET  Initializing external applications
Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3081497520]: NPA: Started thread for network packet analyzer
Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3071007664]: SFP: Started thread for fingerprinting
Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3060517808]: SIH: Started thread for idle hosts detection
Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3050027952]: DNSAR(1): Started thread for DNS address resolution
Thu 21 Dec 2006 07:53:54 PM CET  Calling plugin start functions (if any)
Thu 21 Dec 2006 07:53:54 PM CET  SSL is present but https is disabled: use -W  for enabling it
Thu 21 Dec 2006 07:53:54 PM CET  INITWEB: Initializing web server
.
.
ntop startup - waiting for user response!
.
.
Please enter the password for the admin user: Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3081497520]: NPA: network packet analyzer (packet processor) thread running [p11802]
Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3071007664]: SFP: Fingerprint scan thread starting [p11802]
Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3060517808]: SIH: Idle host scan thread starting [p11802]
Thu 21 Dec 2006 07:53:54 PM CET  THREADMGMT[t3050027952]: DNSAR(1): Address resolution thread running [p11802]`

**ntop will stop there and it is actually waiting for a password. Do so, then it'll prompt to retype it.
 
When done, exit using Ctrl + C

Make sure ntop will start at boot :**
`chkconfig ntop on`

**Start the service :**
`service ntop start`

ntop will start gathering network info.
ntop can be reached at http://server_ip:3000/

I HAVE NOTICED OVER TIME NTOP WAS SLOWING THINGS DOWN ON MY GATEWAY FOR SOME REASON.
I WAS RUNNING ARPWATCH ALONG WITH NTOP AND FOUND ARPWATCH DEAD, MAYBE THE REASON ?

