---
date: 2006-04-01
title: "An OpenLDAP addressbook/directory for Thunderbird"
---

This guide will help you setting up an LDAP directory under RHEL 4/CentOS 4 systems, 100 % compatible with Mozilla Thunderbird 1.5. Management of the LDAP directory will be done with phpLdapAdmin.

**1. Install the needed packages**
`# yum install openldap-servers openldap-clients`

**2. Download the LDAP schema for Thunderbird**
`# wget http://blog.wains.be/pub/thunderbird.schema -O /etc/openldap/schema/thunderbird.schema`

**3. Create the directory tree in which the database will be stored**
`# mkdir /var/lib/ldap/local`

**4. Change ownership**
`# chown ldap:ldap /var/lib/ldap/local`

**5. Make sure LDAP will start at boot**
`# chkconfig --level 345 ldap on`

**6. open tcp port 389 under iptables**
`# iptables -A INPUT -p tcp --dport 389 -j ACCEPT

**7. Make a backup of the default config**
`# mv /etc/openldap/slapd.conf /etc/openldap/slapd.conf.bak`

1. **Create and edit /etc/openldap/slapd.conf :**

    
    <code>include         /etc/openldap/schema/core.schema
    include         /etc/openldap/schema/cosine.schema
    include         /etc/openldap/schema/inetorgperson.schema
    #include         /etc/openldap/schema/nis.schema
    include         /etc/openldap/schema/thunderbird.schema
    
    # bind_v2 will allow compatibility with older Thunderbird clients (tested under v0.4 & v0.5)
    allow bind_v2
    
    pidfile         /var/run/slapd.pid
    argsfile        /var/run/slapd.args
    
    database bdb
    suffix "dc=domain,dc=be"
    rootdn "cn=AddressManager,dc=domain,dc=be"
    rootpw secret
    
    directory /var/lib/ldap/local
    
    index objectClass                       eq,pres
    index ou,cn,mail,surname,givenname      eq,pres,sub
    #index uidNumber,gidNumber,loginShell    eq,pres
    #index uid,memberUid                     eq,pres,sub
    #index nisMapName,nisMapEntry            eq,pres,sub</code>



2. **Start the ldap service :**
`[root@server](1034)# service ldap start    
Checking configuration files for : config file testing succeeded
Starting slapd:                                            [  OK  ]`

10. **Make sure ldap is running (IMPORTANT : if slapd crashes, you'd still get an OK when starting the service) :**
`[root@server](1036)# service ldap status 
slapd (pid 2299) is running...`

11. **Create directory_def.ldif (directory structure) :**
`dn: dc=domain,dc=be
objectclass: top
objectclass: dcObject
objectclass: organization
dc: domain
o: Name of your company` 

12. **Create directory.ldif (directory data) :**
`dn: cn=John Doe,dc=domain,dc=be
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
givenName: John
sn: Doe
cn: John Doe
mail: john.doe@domain.be`

Contacts should be separated by a blank line

13. **Inject the directory structure :**

    
    <code>ldapadd -xv -D "cn=AddressManager,dc=domain,dc=be" -f directory_def.ldif -W
    
    ldap_initialize(  )
    Enter LDAP Password: xxxxxx
    add objectclass:
            top
            dcObject
            organization
    add dc:
            domain
    add o:
            Name of your company
    adding new entry "dc=domain,dc=be"
    modify complete</code>



14. **Inject the data into the directory :**
`ldapadd -xv -D "cn=AddressManager,dc=domain,dc=be" -f directory.ldif -W`

15. **Make sure data have been correctly injected :** 

    
    <code>ldapsearch -x -b "dc=domain,dc=be" "(objectclass=*)"
    
    # extended LDIF
    #
    # LDAPv3
    # base  with scope sub
    # filter: (objectclass=*)
    # requesting: ALL
    #
    
    # domain.be
    dn: dc=domain,dc=be
    objectClass: top
    objectClass: dcObject
    objectClass: organization
    dc: domain
    o: Name of your company
    
    # John Doe, domain.be
    dn: cn=John Doe,dc=domain,dc=be
    objectClass: top
    objectClass: person
    objectClass: organizationalPerson
    objectClass: inetOrgPerson
    givenName: John
    sn: Doe
    cn: John Doe
    mail: john.doe@domain.be</code>



16. **Add the ldap addressbook server into Thunderbird :**

For some reason, setting LDAP autocompletion into the general configuration did not work. 
I had to add the LDAP autocompletion into the account settings

For more info : [http://www.stolaf.edu/services/iit/documentation/thunderbird/ldap.html](http://www.stolaf.edu/services/iit/documentation/thunderbird/ldap.html)

For domain.be :
Name : domain directory
Hostname : ldap.domain.be
Base DN : dc=domain,dc=be

17. **Install phpldapadmin**
- yum install php-ldap
- Grab the RPM version of phpldapadmin that fits to your system on rpmfind.net

SRC RPM : ftp://rpmfind.net/linux/fedora/extras/4/SRPMS/phpldapadmin-0.9.8.2-1.fc4.src.rpm
RPM : ftp://rpmfind.net/linux/fedora/extras/4/i386/phpldapadmin-0.9.8.2-1.fc4.noarch.rpm

- Edit /etc/httpd/conf.d/phpldapadmin and allow connection from your IP
- service httpd restart
- Edit /etc/phpldapadmin/config.php and edit these values :

`$ldapservers->SetValue($i,'server','name','Thunderbird LDAP Server');
$ldapservers->SetValue($i,'server','host','127.0.0.1');
$ldapservers->SetValue($i,'server','port','389');
$ldapservers->SetValue($i,'server','base',array('dc=domain,dc=be'));
$ldapservers->SetValue($i,'server','auth_type','config');
$ldapservers->SetValue($i,'login','dn','cn=AddressManager,dc=domain,dc=be');
$ldapservers->SetValue($i,'login','pass','secret');`

Connect to http://host.domain.be/phpldapadmin/
You should be able to access your LDAP database, please note that this config is not the most secure scheme, you should set a password to access this directory

More info : [http://applications.linux.com/article.pl?sid=05/05/18/1248224](http://applications.linux.com/article.pl?sid=05/05/18/1248224)

Thunderbird can only read into LDAP directories, not write :
[https://bugzilla.mozilla.org/show_bug.cgi?id=86405](https://bugzilla.mozilla.org/show_bug.cgi?id=86405)
