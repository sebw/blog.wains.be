---
date: 2006-11-10
title: "Postfix - deliver emails to the same mailbox from several virtual domains"
---







categories:
- Howto
- Linux
- Postfix


Let's say you own several domains :

**Your default domain : **my-domain.be

**The domain you registered to protect your trademark, copyright, brand, etc :**
- mydomain.be
- my_domain.be



Let's say all these domains point to the same MX server.

We assume your original email addresses are user.name@my-domain.be

What if people try to send to user.name@my_domain.be ?

**What follows is how to setup Postfix to automatically redirect emails to the original domain without headache (no need to setup redirecting aliases on the parked domains).**

We assume the virtual my-domain.be domain is correctly configured and running under Postfix.

Edit /etc/postfix/virtual-mydomain.be
`mydomain.be IGNORE
@mydomain.be @my-domain.be`

postmap /etc/postfix/virtual-mydomain.be

Edit /etc/postfix/virtual-my_domain.be
`my_domain.be IGNORE
@my_domain.be @my-domain.be`

postmap /etc/postfix/virtual-my_domain.be

Edit /etc/postfix/main.cf
`virtual_alias_maps = proxy:mysql:/etc/postfix/path/to/original/virtual/domain,
        hash:/etc/postfix/virtual-my-domain.be,
        hash:/etc/postfix/virtual-mydomain.be`


Now if people send emails to

john.doe@my-domain.be
john.doe@mydomain.be
john.doe@my_domain.be

--> the email will always reach the inbox of John Doe on my-domain.be
