---
date: 2006-09-18
title: "openldap not listening on port 389"
---







categories:
- LDAP
- Linux


For some reason openldap stopped running and listening on port 389 after our server had a problem...

The database files (BDB type) were actually corrupted...

This is how to fix the issue and recover the DB :

- install db4-utils (under Red Hat)
- cd /var/lib/ldap (or whatever directory in which the DB files are located)
- /etc/init.d/ldap stop
- run /usr/sbin/slapd_db_recover in the directory
- /etc/init.d/ldap start

The server was up again after fixing the DB
