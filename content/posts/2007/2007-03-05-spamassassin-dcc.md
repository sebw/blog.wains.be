---
date: 2007-03-05
title: "SpamAssassin + DCC on CentOS/RHEL"
---







categories:
- Howto
- Linux
- Postfix


I decided to give a try to DCC on our CentOS 3.8 mail gateway running Postfix.

SpamAssassin 3.1.8 was already installed.



**Install package :**
`rpm -ihv http://repo.securityteam.us/repository/redhat/el3/i386/RPMS/dcc-1.3.12-1.i386.rpm`

**In order to work properly, DCC needs the following iptables rule :**
`iptables -A INPUT -p udp -m udp --dport 1024:65535 --sport 6277 -j ACCEPT`

**Edit /etc/dcc/dcc_conf and enable dccifd :**
`DCCIFD_ENABLE=on`

**Edit /etc/mail/spamassassin/local.cf :**
`use_dcc 1
dcc_home /etc/dcc`

**Edit /etc/mail/spamassassin/v310.pre :**
`loadplugin Mail::SpamAssassin::Plugin::DCC
dcc_path /usr/bin/dccproc`

**Restart SpamAssassin :**
`service spamassassin restart`

**Test and debug SpamAssassin and see if anything goes wrong with DCC :**
`spamassassin -t -D < /usr/share/doc/spamassassin-3.1.8/sample-spam.txt`

**Output (snippet), see DCC check works :**
``Content analysis details:   (1001.4 points, 3.0 required)
 pts rule name              description
---- ---------------------- --------------------------------------------------
-0.0 NO_RELAYS              Informational: message was not relayed via SMTP
1000 GTUBE                  BODY: Generic Test for Unsolicited Bulk Email
 1.4 DCC_CHECK              Listed in DCC (http://rhyolite.com/anti-spam/dcc/)
-0.0 NO_RECEIVED            Informational: message has no Received headers`
