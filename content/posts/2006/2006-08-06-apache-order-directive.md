---
date: 2006-08-06
title: "Apache Order Directive"
---







categories:
- Apache
- Howto
- Linux


Lately, I had to work around the security of a particular host... which lead me to the Apache docs...

`**Description:**	Controls the default access state and the order in which Allow and Deny are evaluated.
**Default:**	Order Deny,Allow`

The Order directive controls the default access state and the order in which Allow and Deny directives are evaluated. Ordering is one of

**Deny,Allow**
The Deny directives are evaluated before the Allow directives. 
Access is allowed by default. 
Clients which do match a Deny directive will be denied access to the server.
Clients which do match an Allow directive will be allowed access to the server.

Example : 

`Order Deny, Allow
Deny from All
Allow from 192.168.0.0/24`

Unless you want to open the host or directory to the world, always use "deny from all" before your allow directives in this case !!!

**Allow,Deny**
The Allow directives are evaluated before the Deny directives. 
Access is denied by default. 
Clients which do match an Allow directive will be allowed access to the server.
Clients which do match a Deny directive will be denied access to the server.

Example :

`Order Allow,deny
Allow from 192.168.0.0/24`

If you want to allow a specific range but an IP in that range (this is what I needed to do on the host), you should have :

`Order Allow,Deny
Allow from 192.168.0.0/24
Deny from 192.168.0.10`

This example is not possible using "Deny,Allow" : the range would be allowed and evaluated after the deny rule, which would discard the deny rule effect.
