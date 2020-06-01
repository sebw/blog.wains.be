---
date: 2006-08-11
title: "sudo under Red Hat based systems"
---







categories:
- Linux


This will allow users in the wheel group to use sudo without being prompted for the root password :

1. Edit /etc/sudoers
Uncomment the line %WHEEL ALL=(ALL) NOPASSWD: ALL

2. Add a user to the wheel group
`# gpasswd -a johndoe wheel`

where johndoe is the user
