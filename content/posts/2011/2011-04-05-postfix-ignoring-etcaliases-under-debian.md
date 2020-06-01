---
date: 2011-04-05
title: "Postfix ignoring /etc/aliases under Debian ?"
---






categories:
- Debian/Ubuntu
- Linux
- Postfix


So you are running Debian, you added some aliases into /etc/aliases, ran newaliases, but Postfix won't take aliases into account despite what seems to be a correct configuration : 

/etc/postfix/main.cf :
`alias_database = hash:/etc/aliases
alias_maps = hash:/etc/aliases`

/etc/aliases :
`account: someone@example.org`

Check the myorigin parameter :
`# postconf myorigin
myorigin = /etc/mailname`

Now if you check the content of /etc/mailname :
`# cat /etc/mailname
srv.intranet.example.com`

Change that to "intranet.example.com" either in the file or directly under the myorigin setting.

It should not try to deliver locally anymore.

Before :
`Apr  5 14:59:36 srv postfix/local[15144]: DD454425A7: to=, orig_to=, relay=local, delay=0.21, delays=0.14/0.02/0/0.05, dsn=2.0.0, status=sent (forwarded as 5258A425B7)`

After :
`Apr  5 14:59:39 srv postfix/smtp[15145]: F151D42469: to=, orig_to=, relay=smtp.example.com[209.85.143.109]:587, delay=3.4, delay
s=0.05/0.03/1.5/1.8, dsn=2.0.0, status=sent (250 2.0.0 OK 1302008379 t72sm2756613wei.44)`


Link : [http://wiki.debian.org/EtcMailName](http://wiki.debian.org/EtcMailName)
