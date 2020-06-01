---
date: 2006-11-17
title: "Windows Server - dont ever change your admin password !"
---







categories:
- Windows


I guess when you pick up an administrator password on a windows server machine, you've got to stick with it until the end of the world.

For security reasons, I changed the administrator password.
It is like 16 character long now.

After rebooting the machine :
- MS SQL was not starting

It had been set up in NT services to start from the administrator account (don't ask, I didn't set it up that way, a company did).
It didn't notice the password change and could not start

- As a result, services relying on MS SQL could not start either : antivirus, time allowance system, WSUS ?

Ain't that great ?

- Veritas Backup can't connect to its own localhost server

It asks for the credentials but won't let me input more than 12 characters in the password box !

More to come ?

I wish I could dump that Windows machine off the network !
