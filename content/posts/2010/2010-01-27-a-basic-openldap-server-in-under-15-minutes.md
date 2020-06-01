---
date: 2010-01-27
title: "A basic OpenLDAP server in under 15 minutes"
---







categories:
- Debian/Ubuntu
- Howto
- LDAP


_Tested under Debian Lenny_

This howto is basic, as in "no security involved". I may come up with a second part to this guide about securing OpenLDAP with TLS, if I ever find the time.
Meanwhile see [http://www.openldap.org/doc/admin23/security.html](http://www.openldap.org/doc/admin23/security.html) for the security aspect of things.


In this example, I'll create a tree following this scheme : dc=my,dc=domain,dc=tld.
It's really up to you how you organize your tree, it's really for organizational purposes. You can limit your tree to a single root branch, for example dc=myname.

Install OpenLDAP server and some useful utilities :

`# apt-get install slapd ldap-utils`

You should be prompted for a password, if not create it from the command line :

`# slappasswd 
New password: 
Re-enter new password: 
{SSHA}vFk3EP4SSW0RDm4yEKD`

Edit /etc/ldap/slapd.conf :

You should copy the password obtained with slappasswd under the rootpw option.


    
    <code>include         /etc/ldap/schema/core.schema
    include         /etc/ldap/schema/cosine.schema
    include         /etc/ldap/schema/nis.schema
    include         /etc/ldap/schema/inetorgperson.schema
    pidfile         /var/run/slapd/slapd.pid
    argsfile        /var/run/slapd/slapd.args
    loglevel        none
    modulepath	/usr/lib/ldap
    moduleload	back_hdb
    sizelimit 500
    tool-threads 1
    backend		hdb
    database        hdb
    suffix          "dc=my,dc=domain,dc=tld"
    rootdn          "cn=admin,dc=my,dc=domain,dc=tld"
    rootpw		"{SSHA}vFk3EP4SSW0RDm4yEKD"
    directory       "/var/lib/ldap"
    dbconfig set_cachesize 0 2097152 0
    dbconfig set_lk_max_objects 1500
    dbconfig set_lk_max_locks 1500
    dbconfig set_lk_max_lockers 1500
    index           objectClass eq
    lastmod         on
    checkpoint      512 30
    access to attrs=userPassword,shadowLastChange
            by dn="cn=admin,dc=my,dc=domain,dc=tld" write
            by anonymous auth
            by self write
            by * none
    access to dn.base="" by * read
    access to *
            by dn="cn=admin,dc=my,dc=domain,dc=tld" write
            by * read
    </code>



"cn=admin,dc=my,dc=domain,dc=tld" is the database admin. This is what you will use as credential when you need to modify something in the database.

Then edit /etc/ldap/ldap.conf :

This is the configuration for the LDAP client.

`HOST 127.0.0.1
BASE dc=my,dc=domain,dc=tld
URI ldap://localhost`

Now create a directory that will contain some initial configuration files.

`# mkdir /etc/ldap/LDIF`

In this directory create the following files :

1_base.ldif (the base of our LDAP tree) :

`dn: dc=my,dc=domain,dc=tld
dc: my 
objectClass: domain`

2_group.ldif (this will be the branch that will host our groups) :

`dn: ou=Groups,dc=my,dc=domain,dc=tld
ou: Groups
objectClass: organizationalUnit`

3_dev.ldif (this is our first group) :

`dn: cn=dev,ou=Groups,dc=my,dc=domain,dc=tld
cn: dev
gidNumber: 30000
memberUid: user1
objectClass: posixGroup
objectClass: top
description: developers`

4_people.ldif (this is the branch hosting users) :

`dn: ou=People,dc=my,dc=domain,dc=tld
ou: People
objectClass: organizationalUnit`

5_user1.ldif (this is our first user) :

`dn: uid=user1,ou=People,dc=my,dc=domain,dc=tld
uid: user1
cn: John Doe
displayName: John Doe 
givenName: Doe
sn: Doe
objectClass: inetOrgPerson
userPassword: pass
mail: johndoe@domain.tld`


When we are done, we can restart OpenLDAP and create the tree and import some data :

`# /etc/init.d/slapd restart

You should be prompted for the admin password as much as you have LDIF files in the directory.
If you didn't name your file 1_base.ldif, 2_group.ldif, etc. the command may fail as it may try to add a group or user before creating its branch.


Now you should be able to query the LDAP tree :


    
    <code># ldapsearch -x
    # extended LDIF
    #
    # LDAPv3
    # base  (default) with scope subtree
    # filter: (objectclass=*)
    # requesting: ALL
    #
    
    # my.domain.tld
    dn: dc=my,dc=domain,dc=tld
    dc: my
    objectClass: domain
    
    # Groups, my.domain.tld
    dn: ou=Groups,dc=my,dc=domain,dc=tld
    ou: Groups
    objectClass: organizationalUnit
    
    # People, my.domain.tld
    dn: ou=People,dc=my,dc=domain,dc=tld
    ou: People
    objectClass: organizationalUnit
    
    # user1, People, my.domain.tld
    dn: uid=user1,ou=People,dc=my,dc=domain,dc=tld
    uid: user1
    cn: John Doe
    displayName: John Doe
    givenName: Doe
    sn: Doe
    objectClass: inetOrgPerson
    mail: johndoe@domain.tld
    
    # dev, Groups, my.domain.tld
    dn: cn=dev,ou=Groups,dc=my,dc=domain,dc=tld
    cn: dev
    gidNumber: 30000
    memberUid: user1
    objectClass: posixGroup
    objectClass: top
    description: developers
    
    # search result
    search: 2
    result: 0 Success
    
    # numResponses: 6
    # numEntries: 5</code>



This command requires /etc/ldap/ldap.conf. If you don't have ldap.conf configured you'd have to type the whole command :

`# ldapsearch -x -b "dc=my,dc=domain,dc=tld" -H ldap://server`

Now, you can authenticate several services against your new LDAP server.
For example, web authentication in Apache.. Take a look at [http://blog.wains.be/post/apache-simple-authentication-and-ldap-authentication-examples/](http://blog.wains.be/post/apache-simple-authentication-and-ldap-authentication-examples/)

You also may want to install PHP LDAP Admin for managing your LDAP database through a web GUI :

`# apt-get install phpldapadmin`

Then go to http://server/phpldapadmin and authentify with cn=admin,dc=my,dc=domain,dc=tld and your rootpw.


