---
date: 2008-03-18
title: "Backup your Gmail account in maildir format using fetchmail"
---







categories:
- Howto
- Linux
- Misc


**First of all, enable POP on your gmail account :**

- settings
- Forwarding and POP/IMAP
- Enable POP for all mail (even mail that's already been downloaded)

Then, the configuration for fetchmail and procmail :

You need to use procmail in order to be able to store the mails in maildir format (1 file per mail, mbox format is 1 big file for all emails)

**/home/USER/.fetchmailrc :**

`poll pop.gmail.com
        protocol pop3
        username "ACCOUNT@gmail.com" password "PASSWORD"
        keep ssl
        mda "/usr/bin/procmail -m /home/USER/.procmailrc" `

We are using the pop3 protocol, we keep messages on the server and use the SSL server to retrieve our emails from.

**/home/USER/.procmailrc :**


    
    <code>MAILDIR=/home/USER/
    VERBOSE=on
    
    :0
    Mail/</code>



**Initial retrieval :**

The first time, you'll need to run fetchmail in USER's session manually to retrieve your mailbox. Indeed, after a while fetchmail will stop retrieving emails, so you need to run it again a couple of times before it's done. I guess gmail limits the number of emails you can retrieve in a session. You should set up a regular cronjob to retrieve emails automatically, you shouldn't wait too long between two retrievals to avoid missing emails due to the restriction.

**Setting up a cron job in the USER's session to run the backup every night :**

crontab -e

`0 1 * * * /usr/bin/fetchmail > /dev/null 2> /home/USER/Mail/fetchmail.log`

I set up mutt to read my backup emails from the CLI :

**/home/USER/.muttrc :**

`set mbox_type=Maildir
set folder="~/Mail"
set mask="!^.[^.]"
set mbox="~/Mail"
set record="+.Sent"
set postponed="+.Drafts"
set spoolfile="~/Mail"`


