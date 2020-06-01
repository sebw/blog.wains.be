---
date: 2005-11-17
title: "HOWTO - Proftpd + mysql authentication (virtual users) + xinetd"
---

**Compilation & installation**

`wget [http://dag.wieers.com/packages/proftpd/proftpd-1.2.10-8.dag.src.rpm](http://dag.wieers.com/packages/proftpd/proftpd-1.2.10-8.dag.src.rpm)
yum install pkgconfig mysql-devel
rpm -ihv proftpd-1.2.10-8.dag.src.rpm
rpmbuild -ba --with-mysql /usr/src/redhat/SPECS/proftpd-1.2.10-dag.spec`



For the lazy asses out there, this is the compiled version : 
`wget [http://blog.wains.be/pub/proftpd-1.2.10-8_mysql.dag.i386.rpm](http://blog.wains.be/pub/proftpd-1.2.10-8_mysql.dag.i386.rpm)
rpm -ihv proftpd-1.2.10-8_mysql.dag.i386.rpm`

**User configuration**

`groupadd -g 5500 ftpgroup
adduser -u 5500 -s /bin/false -d /bin/null -c "proftpd virtual user" -g ftpgroup ftpuser`

**Building the MySQL database**

`mysql -u root -p
create database proftpd_auth;
grant select, insert, update on proftpd_auth.* to proftpd@localhost identified by 'password';
use proftpd_auth;
CREATE TABLE ftpgroup (
groupname varchar(16) NOT NULL default '',
gid smallint(6) NOT NULL default '5500',
members varchar(16) NOT NULL default '',
KEY groupname (groupname)
) TYPE=MyISAM COMMENT='ProFTP group table';
INSERT INTO `ftpgroup` VALUES ('ftpgroup', 5500, 'ftpuser');
INSERT INTO `ftpgroup` VALUES ('ftpgroup', 5500, 'ftpguest');
CREATE TABLE ftpuser (
id int(10) unsigned NOT NULL auto_increment,
userid varchar(32) NOT NULL default '',
passwd varchar(32) NOT NULL default '',
uid smallint(6) NOT NULL default '5500',
gid smallint(6) NOT NULL default '5500',
homedir varchar(255) NOT NULL default '',
shell varchar(16) NOT NULL default '/sbin/nologin',
count int(11) NOT NULL default '0',
accessed datetime NOT NULL default '0000-00-00 00:00:00',
modified datetime NOT NULL default '0000-00-00 00:00:00',
PRIMARY KEY (id),
UNIQUE KEY userid (userid)
) TYPE=MyISAM COMMENT='ProFTP user table';
INSERT INTO `ftpuser` VALUES (1, 'guest', 'guest', 5500, 5500, '/home/ftp/guest', '/sbin/nologin',0,'','');
exit;`

**ProFTPd configuration file /etc/proftpd.conf :**

`ServerType inetd < -- needed to run proftpd from xinetd
DefaultServer on
PassivePorts 49152 65534 <-- passive ports range needed for passive transaction 
ServerIdent on "" <-- could be anything, here it is turned on with no message (the most secure), if you set it to off, it'll show proftpd
ServerAdmin me
DeferWelcome on <-- secure
Port 21
Umask 022
MaxInstances 10
User      nobody
Group     nobody
DefaultRoot ~ <-- it will jail the users
UseReverseDNS off
AllowStoreRestart       on <-- allow file resume 
#
Directory
AllowOverwrite on
#
### Restrictions, set to your needs
MaxClientsPerHost   2 "Two clients by hostname max"
MaxClientsPerUser   1 "Only one connection per user allowed"
MaxClients   15 "Too many users, please try again later"
MaxHostsPerUser   1 "Only one host per user allowed"
MaxLoginAttempts   5 "You've reached the max. login attempts"
TransferRate RETR   45
#
### Logs options
TransferLog                     /var/log/proftpd/proftpd.xferlog
LogFormat            default "%h %l %u %t "%r" %s %b"
LogFormat            auth    "%v %P %h %t "%r" %s"
LogFormat            write   "%h %l %u %t "%r" %s %b"
ExtendedLog                     /var/log/proftpd/proftpd.access_log    WRITE,READ
ExtendedLog                     /var/log/proftpd/proftpd.auth_log      AUTH auth
ExtendedLog                     /var/log/proftpd/proftpd.paranoid_log  ALL default
### I still need to set up a logrotate script...
#
### MySQL options
### The passwords in MySQL are encrypted using CRYPT
SQLAuthTypes            Plaintext Crypt
SQLAuthenticate         users* groups*
#
### used to connect to the database
### databasename@host database_user user_password
SQLConnectInfo  proftpd_auth@localhost proftpd password
#
### Here we tell ProFTPd the names of the database columns in the "usertable"
### we want it to interact with. Match the names with those in the db
SQLUserInfo     ftpuser userid passwd uid gid homedir shell
#
### Here we tell ProFTPd the names of the database columns in the "grouptable"
### we want it to interact with. Again the names match with those in the db
SQLGroupInfo    ftpgroup groupname gid members
#
### set min UID and GID - otherwise these are 999 each
SQLMinID        500
#
### create a user's home directory on demand if it doesn't exist
SQLHomedirOnDemand on
#
### Update count every time user logs in
SQLLog PASS updatecount
SQLNamedQuery updatecount UPDATE "count=count+1, accessed=now() WHERE userid='%u'" ftpuser
#
### Update modified everytime user uploads or deletes a file
SQLLog  STOR,DELE modified
SQLNamedQuery modified UPDATE "modified=now() WHERE userid='%u'" ftpuser
#
RootLogin off
RequireValidShell off`

**xinetd configuration file /etc/xinetd.d/xproftpd :**

``service ftp
{
        socket_type             = stream
        wait                    = no
        user                    = root
        server                  = /usr/sbin/in.proftpd
        log_on_success          += DURATION USERID
        log_on_failure          += USERID
        nice                    = 10
        disable                 = no
}`

**Tips**

- To make sure MySQL receives your queries, add :
**/etc/my.cnf :**
`log=/var/log/mysqld.log` 

Restart mysqld

- You can always connect to proftpd using linux user accounts, if you want to disable access to users, just add their usernames in /etc/ftpusers

- You can either set password in clear or encrypt under the database, both will be accepted

This guide is widely based on [http://www.khoosys.net/single.htm?ipg=848]
I stripped out the quota part of their guide since I didn't need it
