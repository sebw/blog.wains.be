---
date: 2006-10-07
title: "DNS resolver order"
---







categories:
- Linux


You should edit /etc/host.conf

Edit the line "order"

order hosts,bind : will query your hosts file first, then bind
order bind,hosts : will query bind, and if no result is found, query your hosts file
order bind : will only query bind, pay attention to maintain localhost and 127.0.0 zones ! If not, this would lead to many troubles with many services

