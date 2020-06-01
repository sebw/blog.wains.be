---
date: 2006-11-18
title: "Postfix chroot + MySQL"
---







categories:
- Howto
- Linux
- Postfix
- Security
- SQL


**Scenario :**

You run Postfix non-chrooted with a MySQL DB as backend.

**The issue :**

You can't run Postfix chrooted because the MySQL sock is not located in the chroot area.

**The fix : **



Edit /etc/my.cnf and add this line under the [mysqld] section :
`bind-address = 127.0.0.1`

Restart mysql

Make Postfix connect to MySQL using TCP instead of using sock

Change your SQL files from :
`user = username
password = password
dbname = database
hosts = localhost
query = SELECT alias FROM batabase WHERE id = '%s'`

To :
`user = username
password = password
dbname = database
hosts = 127.0.0.1
query = SELECT alias FROM batabase WHERE id = '%s'`

By changing the line "hosts" from localhost to "127.0.0.1", Postfix will use TCP and won't require access to the sock.
That's the easiest/quickest way of dealing with the problem.

Easily configure Postfix to run chrooted with the script available here : [http://blog.wains.be/pub/postfix-chroot](http://blog.wains.be/pub/postfix-chroot)
