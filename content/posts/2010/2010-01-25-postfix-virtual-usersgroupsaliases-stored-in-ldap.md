---
date: 2010-01-25
title: "Postfix + virtual users/groups/aliases stored in LDAP"
---







categories:
- Howto
- LDAP
- Postfix


This will just explain the configuration files needed for Postfix to check against the LDAP server.

We want to be able to send emails to username@domain.tld
We also want to have aliases for our users, for example : firstname.lastname@domain.tld pointing to username@domain.tld
Finally, we want groups to act as a mailing list, forwarding emails to members of the group, for example : support@domain.tld


**LDAP tree**


    
    <code>dc=domain,dc=tld
    |-------ou=Aliases,dc=domain,dc=tld
    |---------------cn=support,ou=Aliases,dc=domain,dc=tld
    |
    |		cn : support
    |		description : alias support
    |		gidNumber : 50000
    |		mailRoutingAddress : support@domain.tld
    |		memberUid : it (this is a group with a inetLocalMailRecipient class and a mailRoutingAddress field defined)
    |		memberUid : username3 (this is a user account)
    |
    |-------ou=Groups,dc=domain,dc=tld
    |---------------cn=it,ou=Groups,dc=domain,dc=tld
    |
    |		cn : it
    |		description : IT dept group
    |		gidNumber : 40000
    |		mailRoutingAddress : it@domain.tld
    |		memberUid : username1
    |		memberUid : username2
    |
    |-------ou=Users,dc=domain,dc=tld
    |---------------uid=username1,ou=Users,dc=domain,dc=tld
    
    		cn : username1
    		gecos : John Doe
    		gidNumber : 10000
    		homeDirectory : /home/username1
    		mail : john.doe@domain.tld
    		mailLocalAddress : john.doe
    		uid : username1
    		[...]</code>


		

**Postfix configuration**

For this to work, we must define "**append_at_myorigin = yes**" in main.cf
For group/alias emails to work, the group must have the **inetLocalMailRecipient** class and **mailRoutingAddress** defined

So we basically add in main.cf :
`virtual_alias_maps = ldap:/etc/postfix/ldap-account.cf, ldap:/etc/postfix/ldap-group.cf, ldap:/etc/postfix/ldap-alias.cf`

It means that Postfix will check ldap-account.cf first, then ldap-group.cf and finally ldap-alias.cf.


So we create those files :

ldap-account.cf (for virtual users) :

`server_host = localhost 
port = 389
version = 3
search_base = ou=Users,dc=domain,dc=tld
scope = sub
query_filter = (mail=%s)
result_attribute = uid`


ldap-alias.cf (for virtual aliases) :

`server_host = localhost
port = 389
version = 3
scope = sub
search_base = ou=Aliases,dc=domain,dc=tld
query_filter = mailRoutingAddress=%s 
result_attribute = memberUid`


ldap-group.cf (for virtual groups) :

`server_host = localhost
port = 389
version = 3
scope = sub
search_base = ou=Groups,dc=domain,dc=tld
query_filter = mailRoutingAddress=%s 
result_attribute = memberUid`


