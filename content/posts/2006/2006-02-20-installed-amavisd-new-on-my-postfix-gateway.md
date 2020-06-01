---
date: 2006-02-20
title: "Installed amavisd-new on my postfix gateway"
---

I may have been naive but mime_checks used to catch 100 % of any virus sent to us.
Until I found out it was possible to bypass the checks by using special characters like "é", "� " and such in the filenames.. doh !

I decided to install amavisd-new along with clamav.



I'll try to explain how i got the whole thing working, including some personal tips..

You will need several packages, usually downloadable from your regular repo

**Packages needed :**
`clamav-db-0.88-1.1.el3.rf < -- the clamav database
clamd-0.88-1.1.el3.rf <-- includes the clam daemon
clamav-0.88-1.1.el3.rf <-- you'll need this one to update the virus database
spamassassin-3.1.0-1
amavisd-new-2.2.1-1.1.el3.rf

+ a lot of dependencies, generally allowing to extract zip files and such
`

OK, first personal note : if you don't install the clamd package, amavis will complain there's no primary antivirus detected on the system and that it'll use clamscan as a backup solution. It does work fine this way, it'll just complain anytime it gets something to scan. Now, **for better performance, you really should install and run clamd as well as using a unix socket to connect amavis and clamd together.**

I used to run spamassassin for a while and have a quite effective configuration running right now, I decided not to involve spamassassin into amavisd-new.. 
I got rid of anything linked to SA into amavis config file, this can be done on this line of amavis config :
``#@bypass_virus_checks_maps = (1);  # uncomment to DISABLE anti-virus code`

The good think spamassassin with amavis can do is the "$sa_kill_level_deflt = 10" value that will kill any spam reaching that score, SA does not do that by default.

As already mentionned, for better performance, you really should set up clamd to work along with amavis through a UNIX socket.. Look at what is being said under the config file :
`NOTE: the easiest is to run clamd under the same user as amavisd; match the
socket name (LocalSocket) in clamav.conf to the socket name in this entry`

I followed this guide [http://www200.pair.com/mecham/spam/clamav-amavisd-new.html](http://www200.pair.com/mecham/spam/clamav-amavisd-new.html) that explains how to proceed.

Once you run clamav from the amavis user, you could get a few troubles with the log rotations.

This is the cron file for freshclam (moved from /etc/cron.daily by default to /etc/cron.hourly)


    
    
    <code>#!/bin/sh
    
    ### A simple update script for the clamav virus database.
    ### This could as well be replaced by a SysV script.
    
    ### fix log file if needed
    LOG_FILE="/var/log/clamav/freshclam.log"
    if [ ! -f "$LOG_FILE" ]; then
        touch "$LOG_FILE"
        chmod 644 "$LOG_FILE"
        chown clamav.clamav "$LOG_FILE"
    fi
    
    /usr/bin/freshclam 
        --quiet 
        --datadir="/var/clamav" 
        --log="$LOG_FILE" 
        --log-verbose 
        --daemon-notify="/etc/clamd.conf"</code>



I just added the line "chown clamav.clamav "$LOG_FILE"" to avoid freshclam logs to get owned by amavis user on the next rotation.

OK, this is my /etc/amavisd.conf

    
    
    <code>
    use strict;
    
    #@bypass_virus_checks_maps = (1);  # uncomment to DISABLE anti-virus code
    <strong>@bypass_spam_checks_maps  = (1);  # uncomment to DISABLE anti-spam code</strong>
    
    $max_servers = 5;            # number of pre-forked children (2..15 is common)
    <strong>$daemon_user  = "amavis";     # (no default;  customary: vscan or amavis)
    $daemon_group = "amavis";     # (no default;  customary: vscan or amavis)</strong>
    
    $mydomain = 'domain.be';   # a convenient default for other settings
    
    $MYHOME = "/var/amavis";
    $TEMPBASE = "$MYHOME/tmp";   # working directory, needs to be created manually
    $ENV{TMPDIR} = $TEMPBASE;    # environment variable TMPDIR
    $QUARANTINEDIR = "/var/virusmails";
    
    # $daemon_chroot_dir = $MYHOME;   # chroot directory or undef
    
    $db_home   = "$MYHOME/db";
    # $helpers_home = "$MYHOME/var";  # prefer $MYHOME clean and owned by root?
    #$pid_file  = "$MYHOME/var/amavisd.pid";
    #$lock_file = "$MYHOME/var/amavisd.lock";
    #NOTE: create directories $MYHOME/tmp, $MYHOME/var, $MYHOME/db manually
    
    @local_domains_maps = ( [".$mydomain"] );
    # @mynetworks = qw( 127.0.0.0/8 ::1 10.0.0.0/8 172.16.0.0/12 192.168.0.0/16 );
    
    $log_level = 1;              # verbosity 0..5
    $log_recip_templ = undef;    # disable by-recipient level-0 log entries
    $DO_SYSLOG = 1;              # log via syslogd (preferred)
    $SYSLOG_LEVEL = 'mail.debug';
    $LOGFILE = "/var/log/amavis.log";
    
    $enable_db = 1;              # enable use of BerkeleyDB/libdb (SNMP and nanny)
    $enable_global_cache = 1;    # enable use of libdb-based cache if $enable_db=1
    
    $inet_socket_port = 10024;   # listen on this local TCP port(s) (see $protocol)
    # $unix_socketname = "$MYHOME/amavisd.sock";  # when using sendmail milter
    
    <strong>$sa_tag_level_deflt  = -999;  # add spam info headers if at, or above that level
    # IF USING SA, SET sa_tag_level_deflt to "-999" for the SA headers to appear anytime</strong>
    $sa_tag2_level_deflt = 3.00; # add 'spam detected' headers at that level
    $sa_kill_level_deflt = 10;   # triggers spam evasive actions
    $sa_dsn_cutoff_level = 10;   # spam level beyond which a DSN is not sent
    
    $sa_mail_body_size_limit = 200*1024; # don't waste time on SA if mail is larger
    $sa_local_tests_only = 0;    # only tests which do not require internet access?
    $sa_auto_whitelist = 1;      # turn on AWL in SA 2.63 or older (irrelevant
                                 # for SA 3.0, cf option is 'use_auto_whitelist')
    
    # @lookup_sql_dsn =
    #   ( ['DBI:mysql:database=mail;host=127.0.0.1;port=3306', 'user1', 'passwd1'],
    #     ['DBI:mysql:database=mail;host=host2', 'username2', 'password2'] );
    
    <strong>$virus_admin               = "admin@$mydomain";  # notifications recip.
    $mailfrom_notify_admin     = "admin@$mydomain";  # notifications sender
    $mailfrom_notify_recip     = "admin@$mydomain";  # notifications sender
    $mailfrom_notify_spamadmin = "admin@$mydomain"; # notifications sender</strong>
    $mailfrom_to_quarantine = ''; # null return path; uses original sender if undef
    
    @addr_extension_virus_maps      = ('virus');
    @addr_extension_spam_maps       = ('spam');
    @addr_extension_banned_maps     = ('banned');
    @addr_extension_bad_header_maps = ('badh');
    # $recipient_delimiter = '+';  # undef disables address extensions altogether
    # when enabling addr extensions do also Postfix/main.cf: recipient_delimiter=+
    
    $path = '/usr/local/sbin:/usr/local/bin:/usr/sbin:/sbin:/usr/bin:/bin';
    $file   = 'file';   # file(1) utility; use recent versions
    $gzip   = 'gzip';
    $bzip2  = 'bzip2';
    $lzop   = 'lzop';
    $rpm2cpio   = ['rpm2cpio.pl','rpm2cpio'];
    $cabextract = 'cabextract';
    $uncompress = ['uncompress', 'gzip -d', 'zcat'];
    $unfreeze   = ['unfreeze', 'freeze -d', 'melt', 'fcat'];
    $arc        = ['nomarch', 'arc'];
    $unarj      = ['arj', 'unarj'];
    $unrar      = ['rar', 'unrar'];
    $zoo    = 'zoo';
    $lha    = 'lha';
    $pax    = 'pax';
    $cpio   = ['gcpio','cpio'];
    $ar     = 'ar';
    $ripole = 'ripole';
    $dspam  = 'dspam';
    
    $MAXLEVELS = 14;
    $MAXFILES = 1500;
    $MIN_EXPANSION_QUOTA =      100*1024;  # bytes  (default undef, not enforced)
    $MAX_EXPANSION_QUOTA = 300*1024*1024;  # bytes  (default undef, not enforced)
    
    $sa_spam_subject_tag = '*** SPAM *** ';
    $defang_virus  = 1;  # MIME-wrap passed infected mail
    $defang_banned = 1;  # MIME-wrap passed mail containing banned name
    
    # OTHER MORE COMMON SETTINGS (defaults may suffice):
    
    <strong>$myhostname = 'domain.be';  # must be a fully-qualified domain name!
    #CHANGE THIS VALUE IF YOU DON'T WANT TO SEE THE WHOLE HOSTNAME ON THE MAIL HEADERS</strong>
    
    # $notify_method  = 'smtp:[127.0.0.1]:10025';
    # $forward_method = 'smtp:[127.0.0.1]:10025';  # set to undef with milter!
    
    # $final_virus_destiny      = D_DISCARD;
    # $final_banned_destiny     = D_BOUNCE;
    # $final_spam_destiny       = D_BOUNCE;
    # $final_bad_header_destiny = D_PASS;
    
    <strong>$final_virus_destiny      = D_DISCARD;
    $final_banned_destiny     = D_DISCARD;
    $final_spam_destiny       = D_DISCARD;
    $final_bad_header_destiny = D_PASS;
    ## I DECIDED TO DISCARD BANNED AND SPAM MAILS</strong>
    
    # SOME OTHER VARIABLES WORTH CONSIDERING (see amavisd.conf-default for all)
    
    # $warnbadhsender,
    # $warnvirusrecip, $warnbannedrecip, $warnbadhrecip, (or @warn*recip_maps)
    #
    # @bypass_virus_checks_maps, @bypass_spam_checks_maps,
    # @bypass_banned_checks_maps, @bypass_header_checks_maps,
    
    <strong>### THIS WILL AVOID SCANNING OUTGOING EMAILS
    @bypass_virus_checks_maps = ( [ "!.$mydomain", "." ] );</strong>
    
    #
    # @virus_lovers_maps, @spam_lovers_maps,
    # @banned_files_lovers_maps, @bad_header_lovers_maps,
    #
    # @blacklist_sender_maps, @score_sender_maps,
    #
    # $virus_quarantine_to, $banned_quarantine_to,
    # $bad_header_quarantine_to, $spam_quarantine_to,
    #
    # $defang_bad_header, $defang_undecipherable, $defang_spam
    
    
    # REMAINING IMPORTANT VARIABLES ARE LISTED HERE BECAUSE OF LONGER ASSIGNMENTS
    
    @viruses_that_fake_sender_maps = (new_RE(
    # [qr'bEICARb'i => 0],            # av test pattern name
    # [qr'^(WM97|OF97|Joke.)'i => 0],  # adjust names to match your AV scanner
      [qr/.*/ => 1],  # true for everything else
    ));
    
    @keep_decoded_original_maps = (new_RE(
    # qr'^MAIL$',   # retain full original message for virus checking (can be slow)
      qr'^MAIL-UNDECIPHERABLE$', # recheck full mail if it contains undecipherables
      qr'^(ASCII(?! cpio)|text|uuencoded|xxencoded|binhex)'i,
    # qr'^Zip archive data',     # don't trust Archive::Zip
    ));
    
    
    # for $banned_namepath_re, a new-style of banned table, see amavisd.conf-sample
    
    $banned_filename_re = new_RE(
    # qr'^UNDECIPHERABLE$',  # is or contains any undecipherable components
    
      # block certain double extensions anywhere in the base name
      qr'.[^./]*.(exe|vbs|pif|scr|bat|cmd|com|cpl|dll).?$'i,
    
    # qr'[{}]',      # curly braces in names (serve as Class ID extensions - CLSID)
    
      qr'^application/x-msdownload$'i,                  # block these MIME types
      qr'^application/x-msdos-program$'i,
      qr'^application/hta$'i,
    
    # qr'^message/partial$'i, qr'^message/external-body$'i, # rfc2046 MIME types
    
    # [ qr'^.(Z|gz|bz2)$'           => 0 ],  # allow any in Unix-compressed
      [ qr'^.(rpm|cpio|tar)$'       => 0 ],  # allow any in Unix-type archives
    # [ qr'^.(zip|rar|arc|arj|zoo)$'=> 0 ],  # allow any within such archives
    
      qr'..(exe|vbs|pif|scr|bat|cmd|com|cpl)$'i, # banned extension - basic
    # qr'..(ade|adp|app|bas|bat|chm|cmd|com|cpl|crt|emf|exe|fxp|grp|hlp|hta|
    #        inf|ins|isp|js|jse|lnk|mda|mdb|mde|mdw|mdt|mdz|msc|msi|msp|mst|
    #        ops|pcd|pif|prg|reg|scr|sct|shb|shs|vb|vbe|vbs|
    #        wmf|wsc|wsf|wsh)$'ix,  # banned ext - long
    
    # qr'..(mim|b64|bhx|hqx|xxe|uu|uue)$'i,  # banned extension - WinZip vulnerab.
    
      qr'^.(exe-ms)$',                       # banned file(1) types
    # qr'^.(exe|lha|tnef|cab|dll)$',         # banned file(1) types
    );
    # See http://support.microsoft.com/default.aspx?scid=kb;EN-US;q262631
    # and http://www.cknow.com/vtutor/vtextensions.htm
    
    
    # ENVELOPE SENDER SOFT-WHITELISTING / SOFT-BLACKLISTING
    
    @score_sender_maps = ({ # a by-recipient hash lookup table,
                            # results from all matching recipient tables are summed
    
    # ## per-recipient personal tables  (NOTE: positive: black, negative: white)
    # 'user1@example.com'  => [{'bla-mobile.press@example.com' => 10.0}],
    # 'user3@example.com'  => [{'.ebay.com'                 => -3.0}],
    # 'user4@example.com'  => [{'cleargreen@cleargreen.com' => -7.0,
    #                           '.cleargreen.com'           => -5.0}],
    
      ## site-wide opinions about senders (the '.' matches any recipient)
      '.' => [  # the _first_ matching sender determines the score boost
    
       new_RE(  # regexp-type lookup table, just happens to be all soft-blacklist
        [qr'^(bulkmail|offers|cheapbenefits|earnmoney|foryou)@'i         => 5.0],
        [qr'^(greatcasino|investments|lose_weight_today|market.alert)@'i=> 5.0],
        [qr'^(money2you|MyGreenCard|new.tld.registry|opt-out|opt-in)@'i=> 5.0],
        [qr'^(optin|saveonlsmoking2002k|specialoffer|specialoffers)@'i   => 5.0],
        [qr'^(stockalert|stopsnoring|wantsome|workathome|yesitsfree)@'i  => 5.0],
        [qr'^(your_friend|greatoffers)@'i                                => 5.0],
        [qr'^(inkjetplanet|marketopt|MakeMoney)d*@'i                    => 5.0],
       ),
    
    #  read_hash("/var/amavis/sender_scores_sitewide"),
    
       { # a hash-type lookup table (associative array)
         'nobody@cert.org'                        => -3.0,
         'cert-advisory@us-cert.gov'              => -3.0,
         'owner-alert@iss.net'                    => -3.0,
         'slashdot@slashdot.org'                  => -3.0,
         'bugtraq@securityfocus.com'              => -3.0,
         'ntbugtraq@listserv.ntbugtraq.com'       => -3.0,
         'security-alerts@linuxsecurity.com'      => -3.0,
         'mailman-announce-admin@python.org'      => -3.0,
         'amavis-user-admin@lists.sourceforge.net'=> -3.0,
         'notification-return@lists.sophos.com'   => -3.0,
         'owner-postfix-users@postfix.org'        => -3.0,
         'owner-postfix-announce@postfix.org'     => -3.0,
         'owner-sendmail-announce@lists.sendmail.org'   => -3.0,
         'sendmail-announce-request@lists.sendmail.org' => -3.0,
         'donotreply@sendmail.org'                => -3.0,
         'ca+envelope@sendmail.org'               => -3.0,
         'noreply@freshmeat.net'                  => -3.0,
         'owner-technews@postel.acm.org'          => -3.0,
         'ietf-123-owner@loki.ietf.org'           => -3.0,
         'cvs-commits-list-admin@gnome.org'       => -3.0,
         'rt-users-admin@lists.fsck.com'          => -3.0,
         'clp-request@comp.nus.edu.sg'            => -3.0,
         'surveys-errors@lists.nua.ie'            => -3.0,
         'emailnews@genomeweb.com'                => -5.0,
         'yahoo-dev-null@yahoo-inc.com'           => -3.0,
         'returns.groups.yahoo.com'               => -3.0,
         'clusternews@linuxnetworx.com'           => -3.0,
         lc('lvs-users-admin@LinuxVirtualServer.org')    => -3.0,
         lc('owner-textbreakingnews@CNNIMAIL12.CNN.COM') => -5.0,
    
         # soft-blacklisting (positive score)
    ##     'sender@example.net'                     =>  3.0,
    ##     '.example.net'                           =>  1.0,
    
       },
      ],  # end of site-wide tables
    });
    
    
    <strong>@av_scanners = (
    
    #### http://www.clamav.net/
     ['ClamAV-clamd',
       &ask_daemon, ["CONTSCAN {}n", "</strong><strong>/var/run/clamav/clamd.sock</strong>"],
       qr/bOK$/, qr/bFOUND$/,
       qr/^.*?: (?!Infected Archive)(.*) FOUND$/ ],
     # NOTE: the easiest is to run clamd under the same user as amavisd; match the
     # socket name (LocalSocket) in clamav.conf to the socket name in this entry
     # When running chrooted one may prefer: ["CONTSCAN {}n","$MYHOME/clamd"]
    ### THIS IS REALLY IMPORTANT ###
    
    );
    
    @av_scanners_backup = (
    
      ### http://www.clamav.net/   - backs up clamd or Mail::ClamAV
      ['ClamAV-clamscan', 'clamscan',
        "--stdout --disable-summary -r --tempdir=$TEMPBASE {}", [0], [1],
        qr/^.*?: (?!Infected Archive)(.*) FOUND$/ ],
    
    );
    
    
    1;  # insure a defined return
    </code>
    



/etc/clamd.conf :

    
    
    <code>
    LogFile /var/log/clamav/clamd.log
    LogFileMaxSize 0
    LogTime
    LogSyslog
    PidFile /var/run/clamav/clamd.pid
    TemporaryDirectory /var/tmp
    DatabaseDirectory /var/clamav
    <strong>LocalSocket /var/run/clamav/clamd.sock
    FixStaleSocket</strong>
    TCPAddr 127.0.0.1
    MaxConnectionQueueLength 30
    ReadTimeout 300
    <strong>User amavis </strong>
    AllowSupplementaryGroups
    ScanMail
    ArchiveMaxCompressionRatio 300
    ArchiveBlockEncrypted
    ArchiveBlockMax
    </code>
    



