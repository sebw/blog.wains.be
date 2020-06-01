---
date: 2007-08-23
title: "Postfix and DSPAM - var/run/dspam/dspam.sock - No such file or directory"
---







categories:
- Linux
- Postfix


OS : Debian Etch
Postfix : 2.3.8
Dspam : 3.6.8

I was following the official dspam documentation ([http://dspam.nuclearelephant.com/text/README-3.6.7.txt](http://dspam.nuclearelephant.com/text/README-3.6.7.txt)) to set up Postfix and DSPAM.

I had this under** /etc/postfix/master.rc** :


    
    <code>smtp      inet  n       -       n       -       -        smtpd -v
            -o content_filter=lmtp:unix:/var/run/dspam/dspam.sock
    
    localhost:10026 inet  n -       n       -       -        smtpd
            -o content_filter=
            -o receive_override_options=no_unknown_recipient_checks,no_header_body_checks
            -o smtpd_helo_restrictions=
            -o smtpd_client_restrictions=
            -o smtpd_sender_restrictions=
            -o smtpd_recipient_restrictions=permit_mynetworks,reject
            -o mynetworks=127.0.0.0/8
            -o smtpd_authorized_xforward_hosts=127.0.0.0/8</code>



But Postfix would not connect to dspam socket, the logs were returning :

`Aug 22 14:58:23 vm-debian4 postfix/lmtp[24981]: 0FE1F43F7E: to=user at domain.lan, relay=none, delay=0.12, delays=0.08/0.04/0/0, dsn=4.4.1, status=deferred (connect to mx.domain.lan[/var/run/dspam/dspam.sock]: No such file or directory)`

Usually, such a problem can occur if you're running postfix as chrooted, you then need to put the sock file under the chroot (usually /var/spool/postfix/...). It can also occur if the permissions to the socket file are not set properly.
The default Debian install of Postfix doesn't run as chrooted.
You can usually tell if a process is chrooted in master.rc :

This instance would run chrooted :
`smtp      inet  n       -       y       -       -        smtpd`

While this one wouldn't :
`smtp      inet  n       -       n       -       -        smtpd`

As you can notice, the 3rd dash specifies if the process will run chrooted or not...

**The fix !!**

By default, master.rc had this :
`lmtp      unix  -       -       -       -       -       lmtp`

It isn't explicitely declared that lmtp should run "not-chrooted"

Changing to :
`lmtp      unix  -       -       n       -       -       lmtp`

... fixed the issue ! ltmp is now explicitedly not chrooted and postfix and dspam now work together.

Thanks to the guys on #postfix (irc.freenode.net) !
