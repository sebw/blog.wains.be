---
date: 2006-11-15
title: "CentOS/RHEL Postfix SMTP AUTH"
---



**Thanks to Luca Gibelli for [his document about Postfix SMTP AUTH](http://www.nervous.it/txt/Postfix-SMTP-AUTH-4-DUMMIES.html).

It helped me a lot, this article is largely based on his work, I adapted it to match RHEL/CentOS systems.**

## Scenario


Your mail server hosts multiple domains.

You use a MySQL database as backend for user authentication.


Users authenticate to the POP3/IMAP server as:  
username : username@example.org  
password : test123  

You want to allow them to authenticate with SMTP AUTH using the same credentials.

The credentials are stored in a MySQL database in an encrypted form.

You want them to authenticate on a secure channel (TLS)



## What you need

- postfix with MySQL, TLS support (available in the centosplus repository)
- cyrus-sasl (available in the base repo)
- cyrus-sasl-plain (ditto)
- cyrus-sasl-md5 (ditto)
- openssl (ditto)
- pam_mysql (ditto)

pam_mysql is available at : [http://repo.securityteam.us/repository](http://repo.securityteam.us/repository)/  
el3 : [http://repo.securityteam.us/repository/redhat/el3/i386/RPMS/pam_mysql-0.6.2-2.i386.rpm](http://repo.securityteam.us/repository/redhat/el3/i386/RPMS/pam_mysql-0.6.2-2.i386.rpm)  
el4 : [http://repo.securityteam.us/repository/redhat/el4/i386/RPMS/pam_mysql-0.6.2-2.i386.rpm](http://repo.securityteam.us/repository/redhat/el4/i386/RPMS/pam_mysql-0.6.2-2.i386.rpm)



## How to set it up

Edit `/etc/pam.d/smtp`:

```
#%PAM-1.0
#auth       required    pam_stack.so service=system-auth
#account    required    pam_stack.so service=system-auth
auth required pam_mysql.so user=database passwd=password host=127.0.0.1 db=postfix table=mailbox usercolumn=username passwdcolumn=password crypt=1 md5=1
account sufficient pam_mysql.so user=database passwd=password host=127.0.0.1 db=postfix table=mailbox usercolumn=username passwdcolumn=password crypt=1 md5=1
```

Change the info to match your configuration

The auth and account lines could be cut in half in this article.

If you are copy-pasting the text, make sure everything holds on one line on both auth and account statement.


Edit `/usr/lib/sasl2/smtpd.conf`:

```
pwcheck_method: saslauthd
mech_list: PLAIN LOGIN
log_level: 5
```


Make sure "MECH=pam" is present under `/etc/sysconfig/saslauthd`


## Edit 15 dec 2006

IMPORTANT NOTICE FOR RHEL/CENTOS 4 USERS

I'm currently installing a mail server  under CentOS 4.4 while this guide describes the CentOS 3 way
I thought it did not matter but there's a small difference with saslauthd..

Under CentOS 4.x you need to add the following line in /etc/sysconfig/saslauthd:
`FLAGS="-r"`

Without the -r flag, saslauthd would query the MySQL database this way:
`25 Query       SELECT password FROM mailbox WHERE username = 'admin'`

Obviously, the whole email address is stored in the database, with the -r flag, it will query correctly:
`26 Query       SELECT password FROM mailbox WHERE username = 'admin@example.org'`

The man page for saslauthd mentions the -r flag under CentOS 4 only..

-r Combine the realm with the login (with an ’@’ sign in between).  e.g.  login: "foo" realm: "bar" will get passed as login: "foo@bar".  Note that the realm will still be passed, which may lead to unexpected behavior.

## End of edit



Add the following lines to `/etc/postfix/main.cf`:

```
smtpd_sasl_local_domain =
smtpd_sasl_auth_enable = yes
smtpd_sasl_security_options = noanonymous
broken_sasl_auth_clients = yes
```

This will enable SASL authentication to all the smtp ssl instances.

You can disable sasl auth for certain instances under `/etc/postfix/master.rc` if you need

```
10.0.0.1:smtps     inet    n       -       n       -       10      smtpd
        -o smtpd_sasl_auth_enable=no
```

Under `/etc/postfix/main.cf`:

Add "permit_sasl_authenticated" to the "smtpd_recipient_restrictions" section:

```
smtpd_recipient_restrictions =
        ...
        permit_sasl_authenticated
        reject_unauth_destination
```

## Enable TLS

* Finally create the SSL certificate needed by TLS:


Note: leave "challenge password" empty.

Add the following lines to `/etc/postfix/main.cf`:

```
smtpd_tls_auth_only = no
smtp_use_tls = no
smtpd_use_tls = yes
smtp_tls_note_starttls_offer = yes
smtpd_tls_key_file = /etc/postfix/tls/smtpd.key
smtpd_tls_cert_file = /etc/postfix/tls/smtpd.crt
smtpd_tls_CAfile = /etc/postfix/tls/cacert.pem
smtpd_tls_loglevel = 1
smtpd_tls_received_header = yes
smtpd_tls_session_cache_timeout = 3600s
tls_random_source = dev:/dev/urandom
tls_daemon_random_source = dev:/dev/urandom
```

Restart the services

```
service saslauthd restart
service postfix restart
```

Start services at boot

```
chkconfig postfix on
chkconfig saslauthd on
```


## Debugging issues with verbose logs

pam-mysql:

add "verbose=true" to the end of the auth and account lines in `/etc/pam.d/smtp`

saslauthd:

stop the service

start saslauthd manually:

`# saslauthd -a pam -d`



## Links

If you want to run Postfix chrooted with a MySQL backend: **[http://blog.wains.be/?p=161](http://blog.wains.be/?p=161)

If you want to run Postfix chrooted with saslauthd: **[http://blog.wains.be/?p=162](http://blog.wains.be/?p=162)

Script to enable/disable Postfix chroot (tested with Postfix 2.2.8): **[http://blog.wains.be/pub/postfix-chroot](http://blog.wains.be/pub/postfix-chroot)
