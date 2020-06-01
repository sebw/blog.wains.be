---
date: 2007-04-26
title: "Postfix + SpamAssassin + ClamAV with procmail without amavisd-new"
---







categories:
- Howto
- Linux
- Postfix
- Security


I used to use Amavisd-new on our email gateway at work. It sucks. It was a memory hog and was consuming around 400 Mb of swap. It was making it difficult to upgrade clamav, something would break anytime you upgraded. I had a very basic use of amavis, I needed to get rid of it and find a better setup.

Basically, I just wanted to pipe the mails into ClamAV then if no viruses were found, pipe them in SpamAssassin, ideally doing all that from procmail. 

And then someone came up with "clamassassin" : [http://jameslick.com/clamassassin/](http://jameslick.com/clamassassin/)

(available in the unstable Debian repository)

`clamassassin is a simple virus filter wrapper for ClamAV for use in procmail filters and similiar applications. clamassassin's interface is similiar to that of spamassassin, making it easy to implement for those familiar with that tool. clamassassin is designed with an emphasis on security, robustness and simplicity. `

Compile clamassassin as described in the docs.

This is my procmail filter (/etc/postfix/procmail/master.rc) :

    
    <code>SHELL=/bin/sh
    DROPPRIVS=YES
    LINEBUF=32768
    SENDMAILFLAGS="-oi"
    SPAMC="/usr/bin/spamc"
    FORMAIL="/usr/bin/formail"
    
    FROM=""
    SHIFT=1
    
    # Virus checks
    :0fw
    | /etc/postfix/clamassassin/clamassassin
    
    # Virus found --> Sent to catchvirus
    :0
    * ^X-Virus-Status: Yes
    ! catchvirus@domain.be
    
    # No virus found --> SpamAssassin checks
    :0f
    |$SPAMC -u spamd -f -s 125000 -U /var/run/spamd.sock
    
    # Spam score 10+ --> email destroyed
    :0
    * ^X-Spam-Level: **********
    {
      SWITCHRC="/etc/postfix/procmail/killspam.rc"
    }
    
    # Spam score 8-10 --> Sent to catchspam with priority low
    :0
    * ^X-Spam-Level: ********
    {
      SWITCHRC="/etc/postfix/procmail/logspamlow.rc"
    }
    
    # Spam score 6-8 --> Sent to catchspam with priority normal
    :0
    * ^X-Spam-Level: ******
    {
      SWITCHRC="/etc/postfix/procmail/logspamnormal.rc"
    }
    
    # Spam score 0-6 --> Delivered tagged if spam score 3+
    :0
    ! -f $FROM "$@"</code>



If a virus is found, it is sent to a special mailbox, or the mail continues its way in the procmail filter. Depending on the spam score, the procmail script is switching to another procmail script. Those scripts can be downloaded from this post : [Postfix + virtual users/domains : kill or forward spam under SpamAssassin using procmail](http://blog.wains.be/post/postfix-virtual-users-and-domains-kill-spam-under-spamassassin-using-procmail/)

I call the procmail script in Postfix by the following placed in master.rc :

    
    <code>procmail                unix    -       n       n       -       10      pipe
            flags=Rq user=filter argv=/usr/bin/procmail -Y -m /etc/postfix/procmail/master.rc ${sender} ${recipient}
    
    192.168.20.3:smtp      inet    n       -       y       -       10      smtpd
            -o content_filter=procmail
            -o smtpd_sasl_auth_enable=no
            -o smtpd_helo_restrictions=permit_mynetworks,reject_non_fqdn_hostname
            -o smtpd_client_connection_count_limit=5
            -o smtpd_client_connection_rate_limit=5</code>



If you want to rewrite subjects, this is an interesting article :
[http://john.ellingsworth.org/?p=16](http://john.ellingsworth.org/?p=16)

Thanks to mjhall.org for their howto about clamassassin : [http://www.mjhall.org/email.php](http://www.mjhall.org/email.php)

This is the swap space state, can you guess when I stopped using amavis and switched to clamassassin ? 

[![Image Hosted by ImageShack.us](https://blog.wains.be/images/00-imageshack.jpg)
