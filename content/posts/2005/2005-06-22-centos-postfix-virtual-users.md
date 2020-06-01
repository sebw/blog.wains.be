---
date: 2005-06-22
title: "CentOS 3.6 + Postfix 2.0.16 MySQL + Courier-imap + SquirrelMail + vmail + SpamAssassin + attachments filtering + RBL filtering + vacation message + auto disclaimer/signature"
---

I manage a network of around 30 computers (25 active clients win/linux, 7 servers win/linux)

I have implemented an email solution based on CentOS and Postfix to easier mail exchange internally and avoid slowness of our ex host.

This article is not really meant to be an howto but an overview of our current configuration.
It may give you ideas or help figuring out some parameters. Some stuff may be missing as well..

If you have any questions, leave a note..

- SpamAssassin : [http://spamassassin.apache.org](http://spamassassin.apache.org)
- Postfix : [http://www.postfix.org](http://www.postfix.org)
- vmail : [http://www.probsd.net/vmail/](http://www.probsd.net/vmail/)
- SquirrelMail : [http://www.squirrelmail.org/](http://www.squirrelmail.org/)
- Postfix 2.0.16 recompiled with MySQL support : [http://blog.wains.be/pub/postfix-mysqlsupport-2.0.16-14.RHEL3.i386.rpm](http://blog.wains.be/pub/postfix-mysqlsupport-2.0.16-14.RHEL3.i386.rpm)
- MySQL 3.23.58 : from CentOS repository
- vmail : edited to support vacation messages
- SpamAssassin 3.0.4 : from [http://spamassassin.apache.org](http://spamassassin.apache.org), see their notes for RPM compilation
- SquirrelMail 1.4.3a-9 : from CentOS repo
- Courier-imap 3.0.3-1 (+ MySQL support) : from CentOS Contrib repo (should be enabled under yum)

**My IP configuration :**


    eth0 Link encap:Ethernet HWaddr 00:04:75:XX:XX:XX
    inet addr:192.168.250.3 Bcast:192.168.250.255 Mask:255.255.255.0

    eth1 Link encap:Ethernet HWaddr 00:04:75:XX:XX:XX
    inet addr:192.168.254.3 Bcast:192.168.254.255 Mask:255.255.255.0

    eth1:0 Link encap:Ethernet HWaddr 00:04:75:XX:XX:XX
    inet addr:192.168.254.4 Bcast:192.168.254.255 Mask:255.255.255.0



eth0 acts as the incoming interface, eth1 et eth1:0 act as outgoing interfaces, respectively with and without auto signature

**/etc/postfix/main.cf :**


    myhostname = mx.domain.be
    mydomain = domain.be
    myorigin = $mydomain
    mydestination = $mydomain, $transport_maps, $myhostname
    mynetworks = 127.0.0.0/8, 192.168.250.0/24, 192.168.254.0/24

    queue_directory = /var/spool/postfix
    command_directory = /usr/sbin
    daemon_directory = /usr/libexec/postfix
    mailbox_command = /usr/bin/procmail

    inet_interfaces = 127.0.0.1, 192.168.250.3, 192.168.254.3, 192.168.254.4

    sendmail_path = /usr/sbin/sendmail.postfix
    newaliases_path = /usr/bin/newaliases.postfix
    mailq_path = /usr/bin/mailq.postfix

    ## Verify sensitive attachment (rejects .scr, .com, etc.)
    mime_header_checks = regexp:/etc/postfix/mime_checks

    smtpd_banner = mx.domain.be ESMTP
    maximal_queue_lifetime = 2d
    delay_warning_time = 2h
    message_size_limit = 40971520
    mailbox_size_limit = 1048576000
    virtual_mailbox_limit = 1048576000

    setgid_group = postdrop
    mail_owner = postfix
    notify_classes = resource,software,policy,delay

    smtpd_helo_required = yes
    smtpd_client_restrictions =
        permit_mynetworks < -- any internal connection doesn't generate a RBL request
        reject_rbl_client sbl-xbl.spamhaus.org < -- 95 % of spam detected by spamhaus
        reject_rbl_client list.dsbl.org
        reject_rbl_client korea.rominet.net
        reject_rbl_client china.rominet.net
        reject_rbl_client taiwan.rominet.net
        reject_rbl_client hong-kong.rominet.net
        reject_rbl_client relays.ordb.org

    ## MySQL parameters + vmail + vacation
    virtual_mailbox_base = /var/spool/postfix/vmail
    virtual_minimum_uid = 1000
    virtual_mailbox_maps = mysql:/etc/postfix/vmailsql/vmailbox
    virtual_alias_maps = mysql:/etc/postfix/vmailsql/valias
    transport_maps = mysql:/etc/postfix/vmailsql/transport
    virtual_uid_maps = static:1000
    virtual_gid_maps = static:1000
    local_recipient_maps = $virtual_mailbox_maps



**/etc/postfix/master.cf :**


    <code># =========================
    # service type private unpriv chroot wakeup maxproc command + args
    # (yes) (yes) (yes) (never) (100)
    # =========================
    ###### filters
    ## vacation / autoreply
    vacation unix - n n - - pipe
        flags=DRhu user=vacation argv=/var/spool/vacation/vacation.pl
    ## spamassassin
    spamassassin unix - n n - - pipe
        user=nobody argv=/usr/bin/spamc -f -e /usr/sbin/sendmail.postfix -oi -f ${sender} ${recipient}
    ## disclaimer / autosig
    autosignature unix - n n - - pipe
        flags=Rq user=filter argv=/etc/postfix/signature/disclaimer -f ${sender} -- ${recipient}
    ###### end of filters

    ###### smtp connectivity
    ## use the following line for no SA / no sig
    #smtp inet n - n - - smtpd

    ## SA incoming/outgoing + no sig
    #smtp inet n - n - - smtpd -o content_filter=spamassassin

    ## Incoming : spamassassin
    # Outgoing on 192.168.254.3 : with auto sig
    # Outgoing on 192.168.254.4 : without auto sig
    127.0.0.1:smtp inet n - n - - smtpd
    192.168.250.3:smtp inet n - n - - smtpd -o content_filter=spamassassin < -- incoming connections, spamassassin filter
    192.168.254.3:smtp inet n - n - - smtpd -o content_filter=autosignature: <-- outgoing connections, auto sig
    192.168.254.4:smtp inet n - n - - smtpd <-- outgoing connections, IP to use to avoid auto sig



Next, files that will make communicate MySQL and Postfix

**/etc/postfix/vmailsql/transport :**

  user = username
  password = password
  dbname = vmail
  table = domains
  select_field = transport
  where_field = domain
  hosts = localhost

**/etc/postfix/vmailsql/valias :**

    user = username
    password = password
    dbname = vmail
    table = valias
    select_field = alias
    where_field = id
    hosts = localhost

**/etc/postfix/vmailsql/vgid : **

    user = username
    password = password
    dbname = vmail
    table = passwd
    select_field = gid
    where_field = id
    hosts = localhost

**/etc/postfix/vmailsql/vmailbox :**

    user = username
    password = password
    dbname = vmail
    table = passwd
    select_field = maildir
    where_field = id
    hosts = localhost

**/etc/postfix/vmailsql/vuid :**

    user = username
    password = password
    dbname = vmail
    table = passwd
    select_field = uid
    where_field = id
    hosts = localhost

**MySQL vmail database :**


    CREATE TABLE `admin` (
     `id` varchar(16) NOT NULL default '',
     `pw` varchar(64) NOT NULL default '',
     PRIMARY KEY  (`id`),
     UNIQUE KEY `id` (`id`)
    ) TYPE=MyISAM;

    CREATE TABLE `deleted` (
     `id` varchar(255) default NULL
    ) TYPE=MyISAM;

    CREATE TABLE `domains` (
     `domain` varchar(128) NOT NULL default '',
     `pw` varchar(64) NOT NULL default '',
     `transport` varchar(10) default 'virtual',
     `ulimit` int(6) default '0',
     `defquota` varchar(32) default NULL,
     `aka` text,
     PRIMARY KEY  (`domain`),
     UNIQUE KEY `domain` (`domain`),
     KEY `domain_2` (`domain`)
    ) TYPE=MyISAM;

    CREATE TABLE `new` (
     `id` varchar(255) default NULL
    ) TYPE=MyISAM;

    CREATE TABLE `passwd` (
     `id` varchar(255) NOT NULL default '',
     `crypt` varchar(64) NOT NULL default '',
     `uid` int(4) NOT NULL default '0',
     `gid` int(4) NOT NULL default '0',
     `home` varchar(255) NOT NULL default '',
     `maildir` varchar(255) NOT NULL default '',
     `gecos` varchar(128) default NULL,
     `en` int(1) NOT NULL default '1',
     `quota` varchar(32) default NULL,
     PRIMARY KEY  (`id`),
     UNIQUE KEY `id` (`id`),
     KEY `id_2` (`id`)
    ) TYPE=MyISAM;

    CREATE TABLE `renamed` (
     `id` varchar(255) default NULL,
     `newid` varchar(255) default NULL
    ) TYPE=MyISAM;

    CREATE TABLE `vacation` (
     `email` varchar(255) NOT NULL default '',
     `subject` varchar(255) NOT NULL default '',
     `body` text NOT NULL,
     `cache` text NOT NULL,
     `domain` varchar(255) NOT NULL default '',
     `created` datetime NOT NULL default '0000-00-00 00:00:00',
     `active` tinyint(1) NOT NULL default '1',
     PRIMARY KEY  (`email`),
     KEY `email` (`email`)
    ) TYPE=MyISAM COMMENT='Postfix Admin - Virtual Vacation';

    CREATE TABLE `valias` (
     `id` varchar(255) NOT NULL default '',
     `alias` varchar(255) NOT NULL default '',
     PRIMARY KEY  (`id`),
     UNIQUE KEY `id` (`id`),
     KEY `id_2` (`id`)
    ) TYPE=MyISAM;



**Add this to root's crontab :**

    */5 * * * * /directory/to/vmail/scripts/./maintain
    */5 * * * * /bin/chown vmail:vmail -R /var/spool/postfix/vmail/* (directory specified in virtual_mailbox_base)
    */5 * * * * /bin/chmod 755 -R /var/spool/postfix/vmail/* (dito)`

These crons will run the maintenance scripts needed by vmail to keep track of adding/deletions/modifications made to users, domains, etc.
It simply means you need to wait for cron to run to see a new user appears, or you can always run the script by hand (that's what I usually do, I don't enable the cron).

**Vmail scripts :**

Wed Jun 22 14:42:01 => /directory/to/vmail/scripts
root(1034)# l
total 24K
-rwxr-xr-x 1 root root 4.8K Mar 12 22:28 maintain
-rwxr-xr-x 1 root root 635 Jan 25 2003 pfsqlfiles
-rwxr-xr-x 1 root root 6.0K Mar 12 22:29 vmadmin
-rwxr-xr-x 1 root root 1.0K Jan 25 2003 vmail.sql

You need to edit these files to match your config

Ex : /directory/to/vmail/scripts/maintain:

    #########################
    $basedir = '/var/spool/postfix/vmail';
    $dbhost = 'localhost';
    $db = 'vmail';
    $dbuser = 'username';
    $dbpw = 'password';
    $maildirmake = '/usr/lib/courier-imap/bin/maildirmake'; < -- depends on your config
    #########################

**courier-imap configuration :**

We need to enable POP3 and IMAPD service and MySQL for courier-imap to be able to fetch users information :

Edit **imapd **and **pop3d **files and make sure POP3DSTART=YES and IMAPDSTART=YES are set. You can disable POP3D-SSL and IMAPD-SSL if you don't need them.

Edit **authdaemonrc **(should be under /usr/lib/courier-imap/etc) et modify these 2 values :

    authmodulelist="authmysql authpam"
    authmodulelistorig="authmysql authpam"

Make sure ports tcp/110 and tcp/143 are not blocked by your firewall.

**Vacation message :**

For auto-replies, I use a script I grabbed from the tool "Postfix Admin" (available at [http://high5.net/postfixadmin/](http://high5.net/postfixadmin/)). Download the latest version and get vacation.pl.

Edit **vacation.pl :** to match your configuration...

    my $db_type = 'mysql';
    my $db_host = 'localhost';
    my $db_user = '';
    my $db_pass = '';
    my $db_name = 'vmail';
    my $sendmail = "/usr/sbin/sendmail";
    my $logfile = ""; # specify a file name here for example: vacation.log
    my $debugfile = ""; # sepcify a file name here for example: vacation.debug
    my $syslog = 0; # 1 if log entries should be sent to syslog

The filter named "vacation" under master.cf will call this script as soon as a mail is sent to user@autoreply.domain.be.

Enabling autoreply on a mailbox :
Okay, let's say we've got this setup for this user and we want to enable an autoreply
Email (alias) : jean.dupont@domain.be
POP account : jdupont@domain.be
Autoreply address jdupont@autoreply.domain.be

You'll need to setup postfix to make it send a copy on jdupont@domain.be and jdupont@autoreply.domain.be (fictive domainname) of any mail sent to jean.dupont@domain.be

Follow me ? Right.. let's recap :

    Email sent to jean.dupont@domain.be
    --> postfix receives the message
    ---> postfix delivers the message to jdupont@domain.be
    ---> postfix virtually "delivers" the message to jdupont@autoreply.domain.be

Upon reception of a message on autoreply.domain.be, postfix will call the vacation script that will send a message back to the sender of the mail. The script is developped to keep track of senders, it avoids an autoreply to be generated at every incoming mail from a particular contact.

**Adding an automatic signature or disclaimer to outgoing mails**

In order to add an autosig or disclaimer (like banks do) at the end of outgoing email, you just need to use Altermime available at  [http://www.pldaniels.com/altermime/](http://www.pldaniels.com/altermime/).

Once compiled and installed, postfix will call the filter called "autosignature" (see master.cf) which will execute a perl script that will check if the outgoing mail is either HTML or TXT. Indeed, there are two different type of disclaimer, an HTML and TXT version.

**Filtering sensitive attachments**

**/etc/postfix/mime_checks :**

    /^s*Content-(Disposition|Type).*names*=s*"?(.+.(com|zip|exe|scr|pif))"?s*$/ REJECT *** FOR SECURITY REASONS, ".$3" FILETYPES ARE AUTOMATICALLY REJECTED BY THIS SERVER, "$2" WAS REJECTED AS IT MAY CONTAIN VIRUSES ***

Please be aware this method won't let any mail with the specified files attached get into your system. The mail will be rejected and a notification will be sent to the sender with the REJECT message.

Now run :
`postalias mime_checks`. It'll generate mime_checks.db

**Webmail : SquirrelMail**

Easy, you just need to install the package squirrelmail from CentOS repositories

After installation, reboot your webserver to apply squirrelmail configuration (actually it's just an alias added in your webserver config, the URL would be http://www.domain.be/webmail/)

Run /usr/share/squirrelmail/config/./conf.pl and edit to match your config

You can add or remove plugins

I use and strongly recommend the SSL plugin that allows secure connections to squirrelmail.
Check this website [http://slacksite.com/apache/certificate.html](http://slacksite.com/apache/certificate.html) to figure out how to add a SSL key to your website

**Queue management under Postfix**

I use pfqueue that easily manage postfix queues

Available at [http://pfqueue.sourceforge.net/](http://pfqueue.sourceforge.net/)

**Queues graphs**

In order to easily check out my queues status, I use queuegraph

queuegraph : [http://www.stahl.bau.tu-bs.de/~hildeb/postfix/queuegraph/](http://www.stahl.bau.tu-bs.de/~hildeb/postfix/queuegraph/)
mailgraph : [http://people.ee.ethz.ch/~dws/software/mailgraph/](http://people.ee.ethz.ch/~dws/software/mailgraph/)

**RBL**

I use this script to generate a daily RBL use reports. This script is not available online, contact me if you want a copy.

This is the output of the script :

        :: RBL STATS for Dec 13 ::
    ------------------------------------
      LIST                        COUNT
    ------------------------------------
      sbl-xbl.spamhaus.org ...: 199
      list.dsbl.org ..........: 2
      spamcop.net ............: 4
    ====================================
      TOTAL:                    205
