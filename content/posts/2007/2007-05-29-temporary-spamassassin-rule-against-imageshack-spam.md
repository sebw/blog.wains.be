---
date: 2007-05-29
title: "Temporary SpamAssassin rule against Imageshack spam"
---







categories:
- Howto


This would help blocking the new imageshack spam while we are waiting for a rule to be directly integrated into SpamAssassin :



**Create the new rule in a separate file :**
`$ vi /etc/mail/spamassassin/imageshack.cf`

`body __IMAGESHACK_URL /.*http://imgd+.imageshack.us.*/
meta SPAMSHACK __IMAGESHACK_URL
describe SPAMSHACK ImageShack Spam ?
score    SPAMSHACK 10.00`

**Run spamassassin lint mode to check if everything is fine :**
`$ spamassassin --lint`

**A test file :**

    
    <code>Received: from [200.121.186.163] (helo=client-200.121.186.163.speedy.net.pe)
            by newhq.calacode.com with esmtp (Exim 4.60)
            (envelope-from )
            id 1G0qqW-0000zu-4c
            for john@calacode.com; Wed, 12 Jul 2006 19:22:37 -0700
    Date: Thu, 13 Jul 2006 02:23:54 +0300
    From: "Lance Leblanc" 
    X-Mailer: The Bat! (v2.11.03) Business
    Reply-To: "Lance Leblanc" 
    X-Priority: 3 (Normal)
    Message-ID: <6905858512.20060713022354@0451.com>
    To: john@calacode.com
    MIME-Version: 1.0
    Content-Type: text/plain; charset=us-ascii
    Content-Transfer-Encoding: 7bit
    Subject: 
    X-UIDL: 1152757358epuszhma1u
    
    Bemonster not thy feature. SWF file, you can't import it in the Flash
    editor, coz it's protected.
    And though they were making poor time, the heavy load they dragged
    sapped their strength severely.
    http://img508.imageshack.us/my.php?image=34795328pu9.gif
    Immense gains.</code>



**Test your new rule :**
`$ spamassassin -t -D < spam-mail-imageshack.txt `

...

**Output : **
``Content analysis details:   (16.3 points, 3.0 required)
 pts rule name              description
---- ---------------------- --------------------------------------------------
 0.1 RDNS_NONE              Delivered to trusted network by a host with no rDNS
 2.9 HELO_DYNAMIC_IPADDR    Relay HELO'd using suspicious hostname (IP addr
                            1)
 3.5 REPTO_OVERQUOTE_THEBAT The Bat! doesn't do quoting like this
 1.3 MISSING_SUBJECT        Missing Subject: header
  10 SPAMSHACK              ImageShack Spam ?
-1.5 AWL                    AWL: From: address is in the auto white-list`

Any email containing a link to imageshack would then be marked as spam.
I'm pretty sure the apache foundation will come up with a new release anytime soon and thus do not feel the need to have something "perfect".
