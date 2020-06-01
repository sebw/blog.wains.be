---
date: 2006-01-16
title: "Script - courier-imap virtual mail accounts listing"
---

I made this little script to get a daily report of the usage of our virtual mailboxes at work, the POP3 server is courier-imap.
I don't need to run any quota on the accounts but I just want to make sure people regularly check their mailboxes.

    
    <code>#!/bin/sh
    
    # Check storage used by virtual mail accounts under courier-imap
    
    DOMAINS="domain1.be domain2.be"
    TIMESTAMP=`date +%d/%m/%Y`
    
    echo "REPORT DATE : $TIMESTAMP"
    echo " "
    for domains in $DOMAINS
    
    do
    echo "---------------------------"
    echo "$domains"
    echo "===================================================="
    echo "Size               Accounts"
    echo "---------------------------"
    echo " "
    du -h /var/spool/postfix/vmail/$domains/ --max-depth=2 -c | 
            egrep "/var/spool/postfix/vmail/$domains/[a-z]/" | 
            sed -re "s//var/spool/postfix/vmail/$domains/[a-z]///g" | 
            egrep "[0-9]{2,3}M" | 
            awk '{print $1, "               ",$2}' | 
            sort -r
    
    du -h /var/spool/postfix/vmail/$domains/ --max-depth=2 -c | 
            egrep "/var/spool/postfix/vmail/$domains/[a-z]/" | 
            sed -re "s//var/spool/postfix/vmail/$domains/[a-z]///g" | 
            egrep "[0-9]{1}.[0-9]{1}M" | 
            awk '{print $1, "       ",$2}' | 
            sort -r
    
    du -h /var/spool/postfix/vmail/$domains/ --max-depth=2 -c | 
            egrep "/var/spool/postfix/vmail/$domains/[a-z]/" | 
            sed -re "s//var/spool/postfix/vmail/$domains/[a-z]///g" | 
            egrep "[0-9]{3}K" | 
            awk '{print $1, "       ",$2}' | 
            sort -r
    
    du -h /var/spool/postfix/vmail/$domains/ --max-depth=2 -c | 
            egrep "/var/spool/postfix/vmail/$domains/[a-z]/" | 
            sed -re "s//var/spool/postfix/vmail/$domains/[a-z]///g" | 
            egrep -v "[0-9]{3}" | 
            egrep "[0-9]{2}K" | 
            awk '{print $1, "       ",$2}' | 
            sort -r
    
            echo " "
    done</code>



Output looks like this :

    
    <code>REPORT DATE : 17/01/2006
    
    ---------------------------
    mac-s.be
    ====================================================
    Size             Accounts
    ---------------------------
     
    23M              admin
    1.1M              admin
    221K              admin
    24K              admin
     
    ---------------------------
    label-design.be
    ====================================================
    Size             Accounts
    ---------------------------
     
    20K              admin</code>
