---
date: 2011-04-11
title: "Authenticate Linux Red Hat with Microsoft Active Directory"
---
2011-04-11


_Tested with Active Directory 2003 and RHEL 6.0_

_Tested with Active Directory 2012 and RHEL 7.2 (September 2016)_

**What we want to do :**

- authentication against AD using Winbind and Kerberos
- allowing local and remote (SSH) authentication to members of a specific AD group (linuxadmin)
- allowing members of linuxadmin to use sudo
- UID/GID mapping against AD
- user homedir will be created at first log using pam_mkhomedir
- still possible to log in using local accounts, in case AD is unavailable

**Check if resolution works:**

    # host -t srv _kerberos._tcp.intranet.example.org
    _kerberos._tcp.intranet.example.org has SRV record 0 100 88 ad01.intranet.example.org.
    _kerberos._tcp.intranet.example.org has SRV record 0 100 88 ad02.intranet.example.org.
    _kerberos._tcp.intranet.example.org has SRV record 0 100 88 ad03.intranet.example.org.

**Install necessary packages and enable Winbind at boot:**

    # yum install samba samba-winbind pam_krb5 sudo authconfig krb5-workstation
    # chkconfig winbind on

On RHEL7 for wbinfo you need this:

	# yum install samba-winbind-clients

**Create directory where homedirs will be stored:**

    # mkdir /home/EXAMPLE
    # chmod 0777 /home/EXAMPLE

IMPORTANT : before proceeding, we need to make sure "hostname -f" returns a FQDN, **THE SUBDOMAIN MUST MATCH THE AD DOMAIN**.

	# hostname -f
	srv.intranet.example.org

**Enable authentication:**
	DOMAIN_SHORT=EXAMPLE	DOMAIN_LONG=INTRANET.EXAMPLE.ORG	authconfig \  		--disablecache \  		--enablewinbind \  		--enablewinbindauth \		--smbsecurity=ads \  		--smbworkgroup=${DOMAIN_SHORT} \  		--smbrealm=${DOMAIN_LONG} \  		--enablewinbindusedefaultdomain \  		--winbindtemplatehomedir=/home/${DOMAIN_SHORT}/%U \  		--winbindtemplateshell=/bin/bash \  		--enablekrb5 \  		--krb5realm=${DOMAIN_LONG} \  		--enablekrb5kdcdns \ 		--enablekrb5realmdns \  		--enablelocauthorize \  		--enablemkhomedir \  		--enablepamaccess \  		--updateall

**Under RHEL 5.0, authconfig didn't have the enablemkhomedir and enablepamaccess options. (you'll get "authconfig: error: no such option: --enablemkhomedir")**

Winbind should restart by itself, if not :

`# service winbind restart`

authconfig will modify a couple of files:

- /etc/samba/smb.conf
- /etc/pam.d/system-auth
- /etc/nsswitch.conf
- /etc/krb5.conf

By default, UID/GID will be stored locally, and will differ from one system to another.

**In order to always get the same UID/GID for our AD users/groups, we'll map the ID's against AD, by modifying /etc/samba/smb.conf:**

From :

	   workgroup = EXAMPLE
	   realm = INTRANET.EXAMPLE.ORG
	   security = ads
	   idmap uid = 16777216-33554431
	   idmap gid = 16777216-33554431
	   template homedir = /home/EXAMPLE/%U
	   template shell = /bin/bash
	   winbind use default domain = true
	   winbind offline logon = false

To :

	   workgroup = EXAMPLE
	   realm = INTRANET.EXAMPLE.ORG
	   security = ads
	   idmap domains = EXAMPLE
	   idmap config EXAMPLE:backend      = rid
	   idmap config EXAMPLE:base_rid     = 500
	   idmap config EXAMPLE:range        = 500-1000000
	   #idmap uid = 16777216-33554431
	   #idmap gid = 16777216-33554431
	   template homedir = /home/EXAMPLE/%U
	   template shell = /bin/bash
	   winbind use default domain = true
	   winbind offline logon = false

**Now, in order to only allow members of linuxadmin group, edit:**

- For RHEL5.6 : /etc/pam.d/system-auth  
- For RHEL6.0 : /etc/pam.d/password-auth

I'll also change the default homedir creation umask.


    
    #%PAM-1.0
    # This file is auto-generated.
    # User changes will be destroyed the next time authconfig is run.
    auth        required      pam_env.so
    auth        sufficient    pam_unix.so nullok try_first_pass
    auth        requisite     pam_succeed_if.so user ingroup linuxadmin debug
    auth        requisite     pam_succeed_if.so uid >= 500 quiet
    auth        sufficient    pam_krb5.so use_first_pass
    auth        sufficient    pam_winbind.so use_first_pass
    auth        required      pam_deny.so
    
    account     required      pam_access.so
    account     required      pam_unix.so broken_shadow
    account     sufficient    pam_localuser.so
    account     sufficient    pam_succeed_if.so uid < 500 quiet
    account     [default=bad success=ok user_unknown=ignore] pam_krb5.so
    account     [default=bad success=ok user_unknown=ignore] pam_winbind.so
    account     required      pam_permit.so
    
    password    requisite     pam_cracklib.so try_first_pass retry=3 type=
    password    sufficient    pam_unix.so md5 shadow nullok try_first_pass use_authtok
    password    sufficient    pam_krb5.so use_authtok
    password    sufficient    pam_winbind.so use_authtok
    password    required      pam_deny.so
    
    session     optional      pam_keyinit.so revoke
    session     required      pam_limits.so
    session     optional      pam_mkhomedir.so <strong>umask=0077</strong>
    session     [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
    session     required      pam_unix.so
    session     optional      pam_krb5.so



**Restart Winbind :**

    # service winbind restart 

**Now, join the machine to the domain, in this example user01 has domain admin permissions.**

    # net ads join -U user01
    user01's password:
    Using short domain name -- example
    Joined 'SRV' to realm 'INTRANET.EXAMPLE.ORG'`

When joining the domain, you could get error about DNS updates (maybe because the record already exists). This is not a problem.

**Restart Winbind again :**

    # service winbind restart

**Check if it works, by listing AD groups :**

    # wbinfo -g

**Now, allow users in the linuxadmin group to use sudo :**

    # echo "%linuxadmin ALL=(ALL) ALL" >> /etc/sudoers

**Test authentication using an AD account (in the linuxadmin group) and access to root account :**

On the server check the logs: 
`tail -f /var/log/secure`

On the client:

    $ ssh user01@srv.intranet.example.org
	user01@srv.intranet.example.org's password: 
	Creating directory '/home/EXAMPLE/user01'.
	[user01@srv ~]$ sudo su -
	[sudo] password for user01: 
	[root@srv ~]# `

Test with another account, not being part of linuxadmin group, this time. User should be disconnected.


Logs should look something like this :

    
    Apr 17 17:15:52 x sshd[27114]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=192.168.1.1  user=user-01
    Apr 17 17:15:52 x sshd[27114]: pam_krb5[27114]: authentication succeeds for 'user-01' (user-01@INTRANET.EXAMPLE.ORG)
    Apr 17 17:15:52 x sshd[27114]: pam_winbind(sshd:account): [pamh: 0x7f6910199390] ENTER: pam_sm_acct_mgmt (flags: 0x0000)
    Apr 17 17:15:52 x sshd[27114]: pam_winbind(sshd:account): user 'user-01' granted access
    Apr 17 17:15:52 x sshd[27114]: pam_winbind(sshd:account): [pamh: 0x7f6910199390] LEAVE: pam_sm_acct_mgmt returning 0 (PAM_SUCCESS)
    Apr 17 17:15:52 x sshd[27114]: pam_succeed_if(sshd:account): requirement "user ingroup linuxadmin" was met by user "user-01"
    Apr 17 17:15:52 x sshd[27114]: Accepted password for user-01 from 192.168.1.1 port 59369 ssh2
    Apr 17 17:15:53 x sshd[27114]: pam_unix(sshd:session): session opened for user user-01 by (uid=0)





**Useful commands :**


    
    # wbinfo -n user05
    S-1-5-21-x-x-x-1129 User (1)
    
    # getent passwd user05
    user05:*:1129:519:John Doe:/home/example/user05:/bin/bash
    
    # getent group linuxadmin
    linuxadmin:*:7579:user01,user02,user03,user04
    
    # wbinfo -u
    # wbinfo -g
    
    # wbinfo -D EXAMPLE
    Name              : EXAMPLE
    Alt_Name          : intranet.example.org
    SID               : S-1-5-21-x-x-x
    Active Directory  : Yes
    Native            : Yes
    Primary           : Yes
    Sequence          : -1



**Sources :**
[http://lanestechblog.blogspot.com/2010/11/ad-authentication-with-rhel-6.html](http://lanestechblog.blogspot.com/2010/11/ad-authentication-with-rhel-6.html)
[http://conigliaro.org/post/active-directory-authentication-with-winbind-on-red-hat-linux/](http://conigliaro.org/post/active-directory-authentication-with-winbind-on-red-hat-linux/)

