---
date: 2007-03-07
title: "SpamAssassin + Razor2 on CentOS/RHEL"
---







categories:
- Howto
- Linux


**Install SpamAssassin 3.1.8**

**Install razor-agents and perl-Razor-Agent from dag's**
`http://dag.wieers.com/rpm/packages/razor-agents/`

**Or from Razor homepage :** http://razor.sourceforge.net/



**Restart SpamAssassin :**
`service spamassassin condrestart`

**Run a test to see if razor2 is running (should be enabled by default in v310.pre) :**
`spamassassin -t -D razor2 < /usr/share/doc/spamassassin-3.1.8/sample-spam.txt`

**Output : **
``[1284] dbg: razor2: razor2 is available, version 2.82
 Razor-Log: Computed razorhome from env: /root/.razor
 Razor-Log: Found razorhome: /root/.razor
 Razor-Log: No /root/.razor/razor-agent.conf found, skipping.
 Razor-Log: No razor-agent.conf found, using defaults. 
Mar 07 12:12:24.271433 check[1284]: [ 2] [bootup] Logging initiated LogDebugLevel=9 to stdout
Mar 07 12:12:24.272294 check[1284]: [ 5] computed razorhome=/root/.razor, conf=, ident=/root/.razor/identity
Mar 07 12:12:24.273110 check[1284]: [ 8] Client supported_engines: 4 8
Mar 07 12:12:24.274301 check[1284]: [ 8]  prep_mail done: mail 1 headers=293, mime0=616
Mar 07 12:12:24.275375 check[1284]: [ 5] read_file: 1 items read from /root/.razor/servers.discovery.lst
Mar 07 12:12:24.276360 check[1284]: [ 5] read_file: 2 items read from /root/.razor/servers.nomination.lst
Mar 07 12:12:24.277366 check[1284]: [ 5] read_file: 1 items read from /root/.razor/servers.catalogue.lst
Mar 07 12:12:24.278420 check[1284]: [ 9] Assigning defaults to joy.cloudmark.com
Mar 07 12:12:24.278965 check[1284]: [ 9] Assigning defaults to folly.cloudmark.com
Mar 07 12:12:24.279576 check[1284]: [ 9] Assigning defaults to c101.cloudmark.com
Mar 07 12:12:24.281502 check[1284]: [ 5] read_file: 18 items read from /root/.razor/server.c101.cloudmark.com.conf
Mar 07 12:12:24.283056 check[1284]: [ 5] read_file: 18 items read from /root/.razor/server.c101.cloudmark.com.conf
Mar 07 12:12:24.283656 check[1284]: [ 5] 172228 seconds before closest server discovery
Mar 07 12:12:24.284327 check[1284]: [ 6] c101.cloudmark.com is a Catalogue Server srl 5098; computed min_cf=21, Server se: 3BC8
Mar 07 12:12:24.285131 check[1284]: [ 8] Computed supported_engines: 4 8
Mar 07 12:12:24.285580 check[1284]: [ 8] Using next closest server c101.cloudmark.com:2703, cached info srl 5098
Mar 07 12:12:24.286081 check[1284]: [ 8] mail 1 has no subject
Mar 07 12:12:24.287119 check[1284]: [ 6] preproc: mail 1.0 went from 616 bytes to 503 
Mar 07 12:12:24.287579 check[1284]: [ 6] computing sigs for mail 1.0, len 503
Mar 07 12:12:24.291613 check[1284]: [ 6] Engine (8) didn't produce a signature for mail 1.0
Mar 07 12:12:24.292299 check[1284]: [ 6] skipping whitelist file (empty?): /root/.razor/razor-whitelist
Mar 07 12:12:24.292911 check[1284]: [ 5] Connecting to c101.cloudmark.com ...
Mar 07 12:12:24.580488 check[1284]: [ 8] Connection established
Mar 07 12:12:24.581003 check[1284]: [ 4] c101.cloudmark.com >> 36 server greeting: sn=C&srl=5098&a=l&a=cg&ep4=7542-10
Mar 07 12:12:24.582033 check[1284]: [ 4] c101.cloudmark.com < < 25
Mar 07 12:12:24.582384 check[1284]: [ 6] cn=razor-agents&cv=2.82
Mar 07 12:12:24.583104 check[1284]: [ 6] c101.cloudmark.com is a Catalogue Server srl 5098; computed min_cf=21, Server se: 3BC8
Mar 07 12:12:24.583683 check[1284]: [ 8] Computed supported_engines: 4 8
Mar 07 12:12:24.584279 check[1284]: [ 8] mail 1.0 e4 sig: maD-J2LHO2hbHzZrbOXcsZ6ndsoA
Mar 07 12:12:24.584853 check[1284]: [ 5] mail 1.0 e8 got no sig
Mar 07 12:12:24.585567 check[1284]: [ 8] preparing 1 queries
Mar 07 12:12:24.586216 check[1284]: [ 8] sending 1 batches
Mar 07 12:12:24.586692 check[1284]: [ 4] c101.cloudmark.com <> 12
Mar 07 12:12:25.115660 check[1284]: [ 6] response to sent.2
p=1&cf=100
Mar 07 12:12:25.119597 check[1284]: [ 6] mail 1.0 e=4 sig=maD-J2LHO2hbHzZrbOXcsZ6ndsoA: Is spam: cf 100 >= min_cf 21
Mar 07 12:12:25.121325 check[1284]: [ 7] method 4: mail 1.0: no-contention part, spam=1
Mar 07 12:12:25.123681 check[1284]: [ 7] method 4: mail 1: a non-contention part was spam, mail spam
Mar 07 12:12:25.124754 check[1284]: [ 3] mail 1 is known spam.
Mar 07 12:12:25.126619 check[1284]: [ 5] disconnecting from server c101.cloudmark.com
Mar 07 12:12:25.128449 check[1284]: [ 4] c101.cloudmark.com < < 5
Mar 07 12:12:25.129531 check[1284]: [ 6] a=q
[1284] dbg: razor2: part=0 engine=4 contested=0 confidence=100
[1284] dbg: razor2: results: spam? 1
[1284] dbg: razor2: results: engine 8, highest cf score: 0
[1284] dbg: razor2: results: engine 4, highest cf score: 100
X-Spam-Flag: YES
X-Spam-Checker-Version: SpamAssassin 3.1.8 (2007-02-13) on
        some.machine.be
X-Spam-Level: **************************************************
X-Spam-Status: Yes, score=1004.6 required=3.0 tests=AWL,DCC_CHECK,
        DIGEST_MULTIPLE,GTUBE,NO_RECEIVED,NO_RELAYS,RAZOR2_CF_RANGE_51_100,
        RAZOR2_CF_RANGE_E4_51_100,RAZOR2_CHECK autolearn=disabled version=3.1.8
X-Spam-Report: 
        * -0.0 NO_RELAYS Informational: message was not relayed via SMTP
        * 1000 GTUBE BODY: Generic Test for Unsolicited Bulk Email
        *  1.5 RAZOR2_CF_RANGE_E4_51_100 Razor2 gives engine 4 confidence level
        *      above 50%
        *      [cf: 100]
        *  0.5 RAZOR2_CHECK Listed in Razor2 (http://razor.sf.net/)
        *  0.5 RAZOR2_CF_RANGE_51_100 Razor2 gives confidence level above 50%
        *      [cf: 100]
        *  3.0 DCC_CHECK Listed in DCC (http://rhyolite.com/anti-spam/dcc/)
        *  0.2 DIGEST_MULTIPLE Message hits more than one network digest check
        * -0.0 NO_RECEIVED Informational: message has no Received headers
        * -1.1 AWL AWL: From: address is in the auto white-list
X-Spam-Date: Scanned on Wed, 07 Mar 2007 12:12:25 +0100
Subject: *** SPAM / 1004.6 *** Test spam mail (GTUBE)
Message-ID: 
Date: Wed, 23 Jul 2003 23:30:00 +0200
From: Sender 
To: Recipient 
Precedence: junk
MIME-Version: 1.0
Content-Type: text/plain; charset=us-ascii
Content-Transfer-Encoding: 7bit
X-Spam-Prev-Subject: Test spam mail (GTUBE)
.
This is the GTUBE, the
        Generic
        Test for
        Unsolicited
        Bulk
        Email
If your spam filter supports it, the GTUBE provides a test by which you
can verify that the filter is installed correctly and is detecting incoming
spam. You can send yourself a test mail containing the following string of
characters (in upper case and with no white spaces and line breaks):
.
XJS*C4JDBQADN1.NSBN3*2IDNEN*GTUBE-STANDARD-ANTI-UBE-TEST-EMAIL*C.34X
.
You should send this test mail from an account outside of your network.
.
Spam detection software, running on the system "fedora.grand-hornu.be", has
identified this incoming email as possible spam.  The original message
has been attached to this so you can view it (if it isn't spam) or label
similar future email.  If you have any questions, see
the administrator of that system for details.
.
Content preview:  This is the GTUBE, the Generic Test for Unsolicited Bulk Email
   If your spam filter supports it, the GTUBE provides a test by which you can
   verify that the filter is installed correctly and is detecting incoming spam.
   You can send yourself a test mail containing the following string of characters
   (in upper case and with no white spaces and line breaks): [...] 
.
Content analysis details:   (1004.6 points, 3.0 required)
.
 pts rule name              description
---- ---------------------- --------------------------------------------------
-0.0 NO_RELAYS              Informational: message was not relayed via SMTP
1000 GTUBE                  BODY: Generic Test for Unsolicited Bulk Email
 1.5 RAZOR2_CF_RANGE_E4_51_100 Razor2 gives engine 4 confidence level
                            above 50%
                            [cf: 100]
 0.5 RAZOR2_CHECK           Listed in Razor2 (http://razor.sf.net/)
 0.5 RAZOR2_CF_RANGE_51_100 Razor2 gives confidence level above 50%
                            [cf: 100]
 3.0 DCC_CHECK              Listed in DCC (http://rhyolite.com/anti-spam/dcc/)
 0.2 DIGEST_MULTIPLE        Message hits more than one network digest check
-0.0 NO_RECEIVED            Informational: message has no Received headers
-1.1 AWL                    AWL: From: address is in the auto white-list`

