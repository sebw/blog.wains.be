---
date: 2007-07-13
title: "CentOS - secure OpenLDAP traffic with SSL"
---







categories:
- Howto
- LDAP
- Linux
- Red Hat/CentOS
- Security


I'll consider you already have a database running.
I'll only review how to set up the SSL certificate and key and what to change in the config files.



**1. SSL cert and key**

`# mkdir /etc/openldap/ssl && mkdir /etc/ssl

This would create a self-signed certificate valid for 10 years.

**2. Configure LDAP**

Under /etc/openldap/slapd.conf (server configuration) add (somewhere between include entries and database entries) :
`TLSCertificateFile /etc/ssl/ldap-cert.pem
TLSCertificateKeyFile /etc/openldap/ssl/ldap-key.pem
TLSCACertificateFile /etc/ssl/ldap-cert.pem`


Under /etc/openldap/ldap.conf (client configuration) add at the end of the file :
`URI ldaps://ldap.domain.be:636/
BASE dc=domain,dc=be
TLS_CACERTDIR /etc/openldap/ssl/`

This defines the server to query when using tools from ldap-utils package


Restart OpenLDAP :
`# service ldap restart`

If you want to see the logs, [see this link](http://blog.wains.be/post/openldap-log/)

**3. Firewall**

Open port tcp/636 in your firewall

**4. Test**

From the LDAP server type : 
`ldapsearch -x `

You should get some output. And obviously not "Can't connect to.."

Thanks to what we specified under /etc/ldap/ldap.conf we don't have to specify the host we want to query in the command.

**5. Configure your LDAP client**

Mozilla Thunderbird is great because it can store the self-signed certificates indefinitely. Outlook doesn't (of course).



