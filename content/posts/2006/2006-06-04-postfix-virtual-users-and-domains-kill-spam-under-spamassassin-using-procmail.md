---
date: 2006-06-04
title: "Postfix + virtual users/domains- kill or forward spam under SpamAssassin using procmail"
---

I needed to be able to kill spam reaching a certain score on my email gateway, which only delivers to virtual users and virtual domains...

When delivering locally using the "local" delivery agent, you can easily use procmail by setting "mailbox_command = /usr/bin/procmail -whatever options".. this option doesn't work for the "virtual" delivery agent though.. I'll explain below how to trigger procmail when using the virtual delivery agent :)

The following configuration does this :
If an email reaches the inbound interface on the email gateway, the email will get piped through the "procmail-test" filter, which will run a procmail script.
The procmail-test filter/script will pipe the email through SpamAssassin which will mark the email as spam (or not).
If the email is marked as spam above a score of 4.0 (4 stars ****, check the headers to be sure SpamAssassin puts stars in the headers), the email won't be delivered to its original recipient(s) but forwarded to catchspam@domain.be.. You can get rid of the email using /dev/null instead of forwarding it.

You can combine many analysis.. such as procmail + amavis...



**Edit /etc/postfix/master.cf :**

procmail-test    unix  -       n       n       -       10       pipe
        flags=Rq user=nobody argv=/usr/bin/procmail -Y -m /etc/postfix/procmail/master.rc ${sender} ${recipient}

inbound_interface:smtp      inet    n       -       n       -       10      smtpd
        -o content_filter=procmail-test

**Edit /etc/postfix/procmail/master.rc :**

SHELL=/bin/sh
DROPPRIVS=YES
LINEBUF=32768
SENDMAILFLAGS="-oi"
SPAMC="/usr/bin/spamc"

FROM=""
SHIFT=1

:0f
|$SPAMC -f -U /var/run/spamd.sock

:0
* ^X-Spam-Level: ****
{
  SWITCHRC="/etc/postfix/procmail/logspam.rc"
}

:0
! -f $FROM "$@"

**Edit /etc/postfix/procmail/logspam.rc :**

LOGFILE="/var/log/spamlog"
UMASK=022
LOGABSTRACT="no"
VERBOSE="no"
FORMAIL="/usr/bin/formail"

DATE=`date +%Y%m%d-%H%M%S`
XSPAMLEVEL=`$FORMAIL -zxX-Spam-Level`

:0 Wi
OLDRECIPIENTS=|echo "$@"

SHIFT=1000

LOG="$DATE $$ Level: $XSPAMLEVEL From: $FROM - Orig-To: $OLDRECIPIENTS - Sent-To: catchspam@domain.be
"

:0
! catchspam@domain.be

The spamlog file looks like this :
20060604-214803 8922 Level: ******************* From:  - Orig-To: user1@domain.be - Sent-To: catchspam@domain.be
20060604-215420 9080 Level: ************* From:  - Orig-To: user2@domain.be - Sent-To: catchspam@domain.be
20060604-220528 9413 Level: ******************* From:  - Orig-To: user2@domain.be - Sent-To: catchspam@domain.be

You can combine different things.. like getting rid of anything above 15 but forwarding anything marked between 4.0 and 14.9.. I'll let you figure this out by yourself, given the above explanations, it shouldn't be too hard for you to build that up.

Edit 28 sept 2006

Here's a copy of my procmail scripts :

[http://blog.wains.be/pub/procmail/killspam.rc](http://blog.wains.be/pub/procmail/killspam.rc)
[http://blog.wains.be/pub/procmail/logspamhigh.rc](http://blog.wains.be/pub/procmail/logspamhigh.rc)
[http://blog.wains.be/pub/procmail/logspamlow.rc](http://blog.wains.be/pub/procmail/logspamlow.rc)
[http://blog.wains.be/pub/procmail/logspamnormal.rc](http://blog.wains.be/pub/procmail/logspamnormal.rc)
[http://blog.wains.be/pub/procmail/master.rc](http://blog.wains.be/pub/procmail/master.rc)
