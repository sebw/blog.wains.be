---
date: 2006-12-19
title: "CentOS/RHEL - Web Proxy + Antivirus (ClamAV)"
---







categories:
- Howto
- Linux
- Proxy
- Red Hat/CentOS
- Security


I'll explain here how to setup a web proxy with antivirus capabilities.

We will use these tools : Squid + ClamAV + a patched version of DansGuardian

**The clamav packages provided are now outdated, I'm going to build an updated version as soon as I can**



Squid : [www.squid-cache.org](http://www.squid-cache.org)
ClamAV : [www.clamav.net](http://www.clamav.net)
DansGuardian : [dansguardian.org](http://dansguardian.org)
DansGuardian Antivirus plugin : [http://www.harvest.com.br/asp/afn/dg.nsf](http://http://www.harvest.com.br/asp/afn/dg.nsf)

You can download squid from the default CentOS repository.
I'll consider you already have a functional squid server.

**The requirements for squid are :**
- it should listen on port 3128
- it should only allow requests from localhost

/etc/squid/squid.conf :
`http_port 3128
acl localhost src 127.0.0.0/255.0.0.0
http_access allow localhost
http_access deny all`
**
Install DansGuardian with the antivirus plugin from SecurityTeam.us repo :

Install the SecurityTeamUS repo :**
`rpm -ihv http://repo.securityteam.us/repository/redhat/securityteamus-repo-latest.rpm`

**Install DansGuardian-av and its dependencies (included on SecurityTeamUS) :**

`**yum install dansguardian-av**`

Output :
`Setting up Install Process
Setting up repositories
SecurityTeamUS            100% |=========================|  951 B    00:00     
Reading repository metadata in from local files
primary.xml.gz            100% |=========================|  30 kB    00:00     
SecurityTe: ################################################## 68/68
Added 68 new packages, deleted 0 old in 0.88 seconds
Parsing package install arguments
Resolving Dependencies
--> Populating transaction set with selected packages. Please wait.
---> Downloading header for dansguardian-av to pack into transaction set.
dansguardian-av-2.8.0.6-1 100% |=========================|  24 kB    00:00     
---> Package dansguardian-av.i386 0:2.8.0.6-1 set to be updated
--> Running transaction check
--> Processing Dependency: libclamav.so.1 for package: dansguardian-av
--> Processing Dependency: clamd for package: dansguardian-av
--> Processing Dependency: libesmtp.so.5 for package: dansguardian-av
--> Processing Dependency: libesmtp for package: dansguardian-av
--> Processing Dependency: clamav for package: dansguardian-av
--> Restarting Dependency Resolution with new changes.
--> Populating transaction set with selected packages. Please wait.
---> Downloading header for libesmtp to pack into transaction set.
libesmtp-0.8.12-1.i386.rp 100% |=========================| 4.9 kB    00:00     
---> Package libesmtp.i386 0:0.8.12-1 set to be updated
---> Downloading header for clamd to pack into transaction set.
clamd-0.88.7-1.i386.rpm   100% |=========================| 4.7 kB    00:00     
---> Package clamd.i386 0:0.88.7-1 set to be updated
---> Downloading header for clamav to pack into transaction set.
clamav-0.88.7-1.i386.rpm  100% |=========================| 7.4 kB    00:00     
---> Package clamav.i386 0:0.88.7-1 set to be updated
--> Running transaction check
--> Processing Dependency: clamav-db = 0.88.7-1 for package: clamav
--> Restarting Dependency Resolution with new changes.
--> Populating transaction set with selected packages. Please wait.
---> Downloading header for clamav-db to pack into transaction set.
clamav-db-0.88.7-1.i386.r 100% |=========================| 2.6 kB    00:00     
---> Package clamav-db.i386 0:0.88.7-1 set to be updated
--> Running transaction check
.
Dependencies Resolved
.
=============================================================================
 Package                 Arch       Version          Repository        Size 
=============================================================================
Installing:
 dansguardian-av         i386       2.8.0.6-1        SecurityTeamUS    309 k
Installing for dependencies:
 clamav                  i386       0.88.7-1         SecurityTeamUS    944 k
 clamav-db               i386       0.88.7-1         SecurityTeamUS    7.3 M
 clamd                   i386       0.88.7-1         SecurityTeamUS     64 k
 libesmtp                i386       0.8.12-1         SecurityTeamUS    176 k
.
Transaction Summary
=============================================================================
Install      5 Package(s)         
Update       0 Package(s)         
Remove       0 Package(s)         
Total download size: 8.8 M
Is this ok [y/N]: y`
.
**Start ClamAV daemon :**
`service clamd start`

By default, clamd should listen on 127.0.0.1:3310

**Set up DansGuardian-av this way :**
`reportinglevel = 3
languagedir = '/etc/dansguardian/languages'
language = 'ukenglish'
loglevel = 3
logexceptionhits = on
logfileformat = 1
loglocation = '/var/log/dansguardian/access.log'
filterip =
filterport = 8080
proxyip = 127.0.0.1
proxyport = 3128
nonstandarddelimiter = on
usecustombannedimage = 1
custombannedimagefile = '/etc/dansguardian/transparent1x1.gif'
filtergroups = 1
filtergroupslist = '/etc/dansguardian/filtergroupslist'
bannediplist = '/etc/dansguardian/bannediplist'
exceptioniplist = '/etc/dansguardian/exceptioniplist'
banneduserlist = '/etc/dansguardian/banneduserlist'
exceptionuserlist = '/etc/dansguardian/exceptionuserlist'
showweightedfound = on
weightedphrasemode = 0
urlcachenumber = 3000
urlcacheage = 900
phrasefiltermode = 2
preservecase = 0
hexdecodecontent = 0
forcequicksearch = 0
reverseaddresslookups = off
reverseclientiplookups = off
createlistcachefiles = on
maxuploadsize = -1
maxcontentfiltersize = 256
usernameidmethodproxyauth = on
usernameidmethodident = off
preemptivebanning = on
forwardedfor = off
usexforwardedfor = off
logconnectionhandlingerrors = on
maxchildren = 120
minchildren = 8
minsparechildren = 4
preforkchildren = 6
maxsparechildren = 32
maxagechildren = 500
ipcfilename = '/tmp/.dguardianipc'
urlipcfilename = '/tmp/.dguardianurlipc'
pidfilename = '/var/run/dansguardian.pid'
nodaemon = off
nologger = off
daemonuser = 'nobody'
daemongroup = 'nobody'
softrestart = off
virusscan = on
virusengine = 'clamav'
tricklelength = 32768
forkscanlength = 32768
firsttrickledelay = 10
followingtrickledelay = 10
maxcontentscansize = 41904304
virusscanexceptions = on
urlcachecleanonly = on
virusscannertimeout = 60
notify = 2 # will notify the admin only
emaildomain = 'domain.be'
postmaster = 'admin@domain.be'
emailserver = '127.0.0.1:25'
downloaddir = '/tmp/dgvirus'
clmaxfiles = 1500
clmaxreclevel = 3
clmaxfilesize = 10485760
clblockencryptedarchives = off
cldetectbroken = off
clamdsocket = '127.0.0.1:3310'`

This is my configuration, please review it to match your needs

**Make sure dansguardian will start at boot :**
`chkconfig dansguardian on`

**Start DansGuardian :**
`service dansguardian start`

Now, you can set up your browser preference to use the antivirus web proxy (IP:8080)

**If you want to set dansguardian as a transparent proxy :**
1. Edit /etc/squid/squid.conf and add :
`httpd_accel_host virtual
httpd_accel_port 80
httpd_accel_with_proxy on
httpd_accel_uses_host_header on`

2. Type this at the command prompt (where your local subnet is 10.0.0.0/24 and your LAN interface is eth0) :
`iptables -t nat -A PREROUTING -i eth0 -s 10.0.0.0/24 -p tcp --dport 80 -j REDIRECT --to-port 8080`

3. Save your iptables configuration, type : 
`iptables-save > /etc/sysconfig/iptables`

**Squid logs can be tailed here :**
tail -f /var/log/squid/access.log

**DansGuardian logs can be tailed here :**
tail -f /var/log/dansguardian/access.log
