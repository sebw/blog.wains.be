---
date: 2006-04-04
title: "Postfix + MySQL + proxymap"
---

I discovered I could improve Postfix performances when using MySQL as backend

According to http://spike.porcupine.org/test/html/proxymap.8.html :

_To consolidate the number of open lookup tables  by
sharing  one  open  table among multiple processes.
For example, making mysql  connections  from  every
Postfix daemon process results in "too many connec-
tions" errors._

**Edit /etc/postfix/main.cf : **

    
    <code># Allow proxy for the settings using MySQL
    proxy_read_maps = $virtual_alias_maps $virtual_mailbox_maps $transport_maps $virtual_uid_maps $virtual_gid_maps
    
    # add proxy: in front of any mysql: 
    virtual_mailbox_base = /var/spool/postfix/vmail
    virtual_minimum_uid = 1000
    virtual_mailbox_maps = proxy:mysql:/etc/postfix/vmailsql/vmailbox
    virtual_alias_maps = proxy:mysql:/etc/postfix/vmailsql/valias
    transport_maps = proxy:mysql:/etc/postfix/vmailsql/transport
    virtual_uid_maps = proxy:mysql:/etc/postfix/vmailsql/vuid
    virtual_gid_maps = proxy:mysql:/etc/postfix/vmailsql/vgid
    local_recipient_maps = $virtual_mailbox_maps</code>
