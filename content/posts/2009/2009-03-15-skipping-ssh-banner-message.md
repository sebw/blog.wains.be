---
date: 2009-03-15
title: "Skipping SSH banner message"
---







categories:
- Linux
- SSH


I'm talking about the banner displayed BEFORE connecting, not the MOTD

**By default :**


    
    <code>$ ssh root@server
    
    ***************************************************************************
                                NOTICE TO USERS
    
    
    This computer system is the private property of its owner, whether 
    individual, corporate or government.  It is for authorized use only. 
    Users (authorized or unauthorized) have no explicit or implicit 
    expectation of privacy.  
    
    Any or all uses of this system and all files on this system may be 
    intercepted, monitored, recorded, copied, audited, inspected, and 
    disclosed to your employer, to authorized site, government, and law 
    enforcement personnel, as well as authorized officials of government 
    agencies, both domestic and foreign.  
    
    By using this system, the user consents to such interception, monitoring, 
    recording, copying, auditing, inspection, and disclosure at the 
    discretion of such personnel or officials.  Unauthorized or improper use 
    of this system may result in civil and criminal penalties and 
    administrative or disciplinary action, as appropriate. By continuing to use
    this system you indicate your awareness of and consent to these terms 
    and conditions of use. LOG OFF IMMEDIATELY if you do not agree to the 
    conditions stated in this warning.  
    
    ****************************************************************************
    
    Last login: Sat Mar 14 21:38:01 2009 from mars</code>




**Using ssh -q : **

`$ ssh -q root@server
Last login: Sat Mar 14 21:39:00 2009 from mars`

This is particularly interesting when you use rsync with ssh from a cronjob.. if you don't use -q you'll likely get the banner sent to you by email.
