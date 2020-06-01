---
date: 2006-01-16
title: "Failed to create cache file- maildirwatch"
---

I got this error anytime i was consulting my IMAP mailbox running under courier-imap :

`Jan 16 17:21:24 server imapd: Failed to create cache file: maildirwatch (account@domain)
Jan 16 17:21:24 server imapd: Error: Input/output error
Jan 16 17:21:24 server imapd: Check for proper operation and configuration
Jan 16 17:21:24 server imapd: of the File Access Monitor daemon (famd).

Just add this line to /etc/xinetd.d/sgi_fam :
`flags   = NOLIBWRAP`

Should look like : 
`service sgi_fam
{
        type         = RPC UNLISTED
        socket_type  = stream
        user         = root
        group        = nobody
        server       = /usr/bin/fam
        wait         = yes
        protocol     = tcp
        rpc_version  = 2
        rpc_number   = 391002
        bind         = 127.0.0.1
        flags   = NOLIBWRAP
}`

Make sure portmap is running..

You should no longer get error messages from now on..

More info : [http://www.courier-mta.org/FAQ.html#fam](http://www.courier-mta.org/FAQ.html#fam)
