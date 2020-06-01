---
date: 2010-12-21
title: "Servname not supported for ai_socktype"
---







categories:
- Linux


If you get something like :

`fetchmail: getaddrinfo("pop.gmail.com","pop3s") error: Servname not supported for ai_socktype`

Make sure /etc/services exists and/or is readable (644).


