---
date: 2010-01-26
title: "Apache - simple authentication and LDAP authentication examples"
---







categories:
- Apache
- LDAP


The following are based on an OpenLDAP directory :

**Simple authentication :**

Users credentials are stored in a file created with htpasswd command

`
	AuthType Basic
	AuthName "Authentication"
	AuthUserFile /etc/apache2/passwd-file
	Require user username1 username2
`

If we want to allow all users in passwd-file, use :
`Require valid-user`


**LDAP user authentication :**

We allow user1 and user2 found in the branch ou=People,dc=domain,dc=tld

`
        AuthName "Authentication"
        AuthType Basic
        AuthBasicProvider ldap
        AuthzLDAPAuthoritative on
        AuthLDAPURL ldap://127.0.0.1/ou=People,dc=domain,dc=tld
        Require ldap-user user1 user2
`


**LDAP group authentication :**

We allow all users in the group "support", users are defined in that group under the memberUid field

`
        AuthName "Authentication"
        AuthType Basic
        AuthBasicProvider ldap
        AuthzLDAPAuthoritative on
        AuthLDAPURL ldap://127.0.0.1/ou=People,dc=domain,dc=tld
        AuthLDAPGroupAttribute memberUid
        AuthLDAPGroupAttributeIsDN off
        Require ldap-group cn=support,ou=Groups,dc=domain,dc=tld
`


**Combination of users and group :**

`
        AuthName "Authentication"
        AuthType Basic
        AuthBasicProvider ldap
        AuthzLDAPAuthoritative on
        AuthLDAPURL ldap://127.0.0.1/ou=People,dc=domain,dc=tld
        AuthLDAPGroupAttribute memberUid
        AuthLDAPGroupAttributeIsDN off
	Require ldap-group  cn=support,ou=Groups,dc=domain,dc=tld
	Require ldap-attribute gidNumber=10000
	Require ldap-user user1 user3 user5
`


The following example is doing group authentication against an Active Directory :

`AuthName "Authentication Active Directory"
AuthType Basic
AuthBasicProvider ldap
AuthzLDAPAuthoritative on
AuthLDAPURL "ldap://ad.intranet.example.com/OU=Users,OU=Departement,OU=Subdepartment,DC=intranet,DC=example,DC=com?sAMAccountName?sub?(objectClass=*)" NONE
AuthLDAPGroupAttribute member
AuthLDAPGroupAttributeIsDN on
AuthLDAPBindDN "cn=binduser,cn=users,dc=intranet,dc=example,dc=com"
AuthLDAPBindPassword "bindpassword"
Require ldap-group CN=group1,CN=Users,DC=intranet,DC=example,DC=com`

For some reason the use (or not) of quotes is very important with Active Directory. Members of a group are defined under the "member" attribute.
You also have to add NONE following the string on the AutLDAPURL line, not sure why.


**See also :**

- Conditional web authentication : [https://blog.wains.be/2007/2007-01-26-apache-conditional-http-authentication.md](https://blog.wains.be/2007/2007-01-26-apache-conditional-http-authentication.md)

- Source for this post : [http://www.linux.com/archive/feature/120050?theme=print](http://www.linux.com/archive/feature/120050?theme=print)

Keith in his post is wrong about the "**Satisfy Any**" option, as explained in Apache documentation : [http://httpd.apache.org/docs/2.0/mod/core.html#satisfy](http://httpd.apache.org/docs/2.0/mod/core.html#satisfy) :

`This directive is only useful if access to a particular area is being restricted **by both username/password and client host address**. In this case the default behavior (All) is to require that the client passes the address access restriction and enters a valid username and password. With the Any option the client will be granted access if they either pass the host restriction or enter a valid username and password.`

He seems to explain that the "Satisfy Any" option is necessary when using several "Require" arguments. That's incorrect, all Require arguments are evaluated by default and must pass checks to give access to the resource.
