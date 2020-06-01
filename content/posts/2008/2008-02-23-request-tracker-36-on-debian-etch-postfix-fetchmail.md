---
date: 2008-02-23
title: "Request-Tracker 3.6 on Debian Etch + Postfix + Fetchmail"
---







categories:
- Howto
- Linux


I'll explain how to setup Request-Tracker (RT) on Debian Etch. This howto assumes you can't install RT on the email gateway. I'll use fetchmail to retrieve emails from the mail server and inject them into RT.

**Install Postfix, fetchmail and the deps :**
`apt-get install postfix fetchmail`

Select "Internet Site" when prompted

**Install MySQL Server 5 and its deps :**
`apt-get install mysql-server-5.0`

**Install Request-Tracker Apache2 package, it will install the necessary dependencies :**
`apt-get install rt3.6-apache2`

**Install Request-Tracker 3.6 and its perl deps** :
`apt-get install request-tracker3.6`

**Edit Request Tracker Config file :**

`# vim /etc/request-tracker3.6/RT_SiteConfig.pm`


    
    <code>
    
    # RT_SiteConfig.pm
    #
    # These are the bits you absolutely *must* edit.
    #
    # To find out how, please read
    #   /usr/share/doc/request-tracker3.6/NOTES.Debian
    
    # THE BASICS:
    
    Set($rtname, 'support.example.org');
    Set($Organization, 'example.org');
    
    Set($CorrespondAddress , 'support@example.org');
    Set($CommentAddress , 'support-comment@example.org');
    
    Set($Timezone , 'Europe/Brussels'); # obviously choose what suits you
    
    # THE DATABASE:
    
    Set($DatabaseType, 'mysql'); # e.g. Pg or mysql
    
    # These are the settings we used above when creating the RT database,
    # you MUST set these to what you chose in the section above.
    
    Set($DatabaseUser , 'rtuser');
    Set($DatabasePassword , 'wibble');
    Set($DatabaseName , 'rtdb');
    
    # THE WEBSERVER:
    
    Set($WebPath , "/rt");
    Set($WebBaseURL , "http://host.example.org");
    
    1;</code>



**Create MySQL user :**

First set up root password
`# mysqladmin -u root password myrootpassword`

Create the user "rtuser" :
`mysql -u root -p
mysql> GRANT ALL PRIVILEGES ON rtdb.* TO 'rtuser'@'localhost' IDENTIFIED BY 'wibble'; FLUSH PRIVILEGES; QUIT`


**Set up RT database :**

`# /usr/sbin/rt-setup-database-3.6 --action init --dba root --prompt-for-dba-password`

You should see something like the following :

Password: 
Now creating a database for RT.
Creating mysql database rtdb.
Now populating database schema.
Creating database schema.
readline() on closed filehandle SCHEMA_LOCAL at /usr/sbin/rt-setup-database-3.6 line 223.
Done setting up database schema.
Now inserting database ACLs
Done setting up database ACLs.
Now inserting RT core system objects
Checking for existing system user...not found.  This appears to be a new installation.
Creating system user...done.
Now inserting RT data
Creating Superuser  ACL...done.
Creating groups...3.4.5.6.7.8.9.done.
Creating users...10.12.done.
Creating queues...1.2.done.
Creating ACL...2.3.done.
Creating ScripActions...1.2.3.4.5.6.7.8.9.10.11.12.13.14.15.16.17.done.
Creating ScripConditions...1.2.3.4.5.6.7.8.9.10.done.
Creating templates...1.2.3.4.5.6.7.8.9.10.11.12.done.
Creating scrips...1.2.3.4.5.6.7.8.9.10.11.12.13.14.done.
Creating predefined searches...1.2.3.done.
Done setting up database content.


**Set up Apache2 :**

Add the following to /etc/apache2/sites-enabled/000-default

Paste at the end of the file just before the closing virtualhost tag :

`Include "/etc/request-tracker3.6/apache2-modperl2.conf"
RedirectMatch ^/$ /rt/`

Enable mod rewrite :

`# cd /etc/apache2/mods-enabled/

Restart Apache2 :
`# /etc/init.d/apache2 restart`


**Finally you need to login to RT and grant CreateTicket and ReplyToTicket to the group Everyone.**

http://host.example.org/rt/
Login as user "root"
Password is "password"

Configuration > Global > Group rights


**Set up a queue :**

Configure > Queue > New queue

You can rename the general queue to whatever you want

**Create your first SuperUser :**

Configure > Users > New User 

We'll call it SupportJedi

Then :
Configure > Global > User Rights > Set SuperUser right to SupportJedi


You may want to edit the templates provided by RT by default :

Configuration > Global > Templates

The most interesting templates are autoreply and resolved.


**Setting up fetchmail :**

Edit /etc/default/fetchmail

`# vim /etc/fetchmailrc`

Set START_DAEMON=yes

Prepare log files :

`# touch /var/log/fetchmail.log

Edit /etc/fetchmailrc


    
    <code>set daemon 60
    set invisible
    set no bouncemail
    set no syslog
    set logfile /var/log/fetchmail.log
    
    # support@example.org
    poll pop.example.org 
            protocol pop3 
            username "login-of-support-mailbox" password "verysecretpassword"
            mda "/usr/bin/rt-mailgate-3.6 --queue support --action correspond --url http://support.example.org/rt/"
            no keep
    
    # support-comment@example.org
    poll pop.example.org 
            protocol pop3 
            username "login-of-supportcomment-mailbox" password "verysecretpassword"
            mda "/usr/bin/rt-mailgate-3.6 --queue support --action comment --url http://support.example.org/rt/"
            no keep
    </code>



This howto assumes the mailboxes are created on the email gateway, explaining this would go way beyond the scope of this article.

The mda line tells fetchmail what to do upon reception of a new email.
You can select the queue in which the mail should be injected, the action can either be "correspond" for customer correspondances while "comment" is for internal comments on a ticket. 

**Restart Fetchmail :**

`# /etc/init.d/fetchmail restart`

Logs go in /var/log/fetchmail.log

You can now try to send an email to support@example.org, the mail would be retrieved by fetchmail and injected into RT.
The requestor will receive an autoreply email (see template autoreply).

**Configuration tips**

1. You should set up an "Administrative CC" on the support queue. You'll get an email notice whenever a new ticket is created or a reply from the customer has been received.

2. I noticed that if you create a ticket from the web interface of RT and add an email address in the CC field, the email specified in the CC will not get the email at the creation of the ticket, only on response made to it. (while AdminCC's are notified)

You must create a new scrip in Configuration > Global > Scrips with the following properties :
On Create Notify Ccs with template Correspondence

I personally created a template in which I added the "keep the ticket ID in subject of emails" comment after the content of the ticket.. making CC's aware of the policy.
