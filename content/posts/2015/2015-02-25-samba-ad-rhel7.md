---
date: 2015-02-25
title: "Samba integrated to Active Directory on RHEL7"
---
Date: 2015-02-25

Tested with Active Directory 2003 and RHEL 7.0

For RHEL 6.0 see [here](http://blog.wains.be/post/authenticate-linux-red-hat-with-microsoft-active-directory)

I consider that the server is correctly set up, its hostname should be set accordingly to the Active Directory domain. It should also be synchronised with NTP. A clock drift could cause issues because of Kerberos.

I assume an AD domain "EXAMPLE" (long name: intranet.example.org)

	# host -t srv _kerberos._tcp.intranet.example.org
	_kerberos._tcp.intranet.example.org has SRV record 0 100 88 srv00a.intranet.example.org.
	_kerberos._tcp.intranet.example.org has SRV record 0 100 88 srv00c.intranet.example.org.
	_kerberos._tcp.intranet.example.org has SRV record 0 100 88 srv00b.intranet.example.org.

Install the packages:

	# yum -y install authconfig samba samba-winbind samba-winbind-clients pam_krb5 krb5-workstation oddjob-mkhomedir nscd adcli ntp

Enable the services at boot:

	# systemctl start smb
	# systemctl enable smb
	# systemctl start winbind
	# systemctl enable winbind
	# systemctl start oddjobd 
	# systemctl enable oddjobd
	# systemctl start dbus

Edit /etc/krb5.conf:

	[logging]
	 default = FILE:/var/log/krb5libs.log
	 kdc = FILE:/var/log/krb5kdc.log
	 admin_server = FILE:/var/log/kadmind.log
	
	[libdefaults]
	 default_realm = INTRANET.EXAMPLE.ORG
	 dns_lookup_realm = true
	 dns_lookup_kdc = true
	 ticket_lifetime = 24h
	 renew_lifetime = 7d
	 forwardable = true
	
	[realms]
	 EXAMPLE.COM = {
	  kdc = kerberos.example.com
	  admin_server = kerberos.example.com
	 }
	
	 INTRANET.EXAMPLE.ORG = {
	 }
	
	[domain_realm]
	 .example.com = EXAMPLE.COM
	 example.com = EXAMPLE.COM
	 intranet.example.org = INTRANET.EXAMPLE.ORG
	 .intranet.example.org = INTRANET.EXAMPLE.ORG

Test Kerberos:

	# kinit username@INTRANET.EXAMPLE.ORG
	# klist

username should be domain admin in the Active Directory.

klist should gives this kind of output:

	Ticket cache: FILE:/tmp/krb5cc_0
	Default principal: username@INTRANET.EXAMPLE.ORG

	Valid starting       Expires              Service principal
	02/25/2015 15:23:30  02/26/2015 01:23:30  krbtgt/INTRANET.EXAMPLE.ORG@INTRANET.EXAMPLE.ORG
		renew until 03/04/2015 15:23:28

Delete the Kerberos ticket you just initialized:

	# kdestroy

Edit /etc/samba/smb.conf:

	[global]
	workgroup = EXAMPLE
	realm = INTRANET.EXAMPLE.ORG
	security = ads
	idmap uid = 10000-19999
	idmap gid = 10000-19999
	idmap config EXAMPLE:backend = rid
	idmap config EXAMPLE:range = 10000000-19999999
	;winbind enum users = no
	;winbind enum groups = no
	;winbind separator = +
	winbind use default domain = yes
	winbind offline logon = false
	template homedir = /home/EXAMPLE/%U
	template shell = /bin/bash
	
		server string = Samba Server Version %v
		
		log file = /var/log/samba/log.%m
		log level = 10
		max log size = 50
		passdb backend = tdbsam
	
	[share]
		path = /home/share
		comment = Some cool directory
		writable = yes
		browseable = yes
		# there's a trust between EXAMPLE and EXAMPLE2
		valid users = username EXAMPLE2\username
		directory mask = 0777
		create mask = 0777

Restart Samba:

	# systemctl restart smb

Join the domain:

	# net join -S EXAMPLE -U username

It should work and you can then get information regarding the join:

	# net ads info
	LDAP server: 192.168.0.1
	LDAP server name: SRV00C.intranet.example.org
	Realm: INTRANET.EXAMPLE.ORG
	Bind Path: dc=INTRANET,dc=EXAMPLE,dc=ORG
	LDAP port: 389
	Server time: Wed, 25 Feb 2015 15:27:05 CET
	KDC server: 192.168.0.1
	Server time offset: 0

Create the directory for AD users:
	
	# mkdir /home/EXAMPLE/
	# chmod 0777 /home/EXAMPLE/

Restart Winbind:

	# systemctl restart winbind

Sources:

[redhat.com](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/pdf/Windows_Integration_Guide/Red_Hat_Enterprise_Linux-7-Windows_Integration_Guide-en-US.pdf)
