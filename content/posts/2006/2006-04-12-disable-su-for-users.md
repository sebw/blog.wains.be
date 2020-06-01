---
date: 2006-04-12
title: "Disable su for users"
---

Uncomment the following line to only allow users in the "wheel" group to be able to su :

**Edit /etc/pam.d/su :**

`# Uncomment the following line to require a user to be in the "wheel" group.
auth       required     /lib/security/$ISA/pam_wheel.so use_uid`
