---
date: 2006-06-04
title: "CentOS 4.3 + Postfix 2.2.9 + TLS/SSL + RBL blocking + SpamAssassin 3.1.2 + Amavisd-new + SPF + pop-before-smtp + procmail + vmail + disclaimer"
---

Edit 16 nov 2006 : please consider using SMTP AUTH instead of pop-before-smtp: [https://blog.wains.be/2013/2013-11-14-outbound-postfix-with-sasl-authentication-against-ldap-dovecot/](https://blog.wains.be/2013/2013-11-14-outbound-postfix-with-sasl-authentication-against-ldap-dovecot/)

This is not aimed to be a step by step guide.. This is just a review of my current config with some helpful notes, it is probably meant for already advanced postfix users seeking for tips and tricks.

WordPress may have reformated the code in some weird way.. don't do massive copy paste and hope it works fine straight away, please review everything carefully !!

If you have troubles figuring out something, drop me a line..

**/etc/postfix/access :**

rejected.net REJECT *** mailbox unavailable ***
rejected.com REJECT *** mailbox unavailable ***

all@domain.be		permit_mynetworks,reject
admin@domain.be		permit_mynetworks,reject

**/etc/postfix/aliases :**

root:		real.user@domain.be

**/etc/postfix/main.cf :**

myhostname = mx.domain.be
mydomain = domain.be
myorigin = $mydomain
mydestination = $mydomain, $transport_maps, $myhostname
mynetworks = 127.0.0.0/8, 192.168.250.0/24, 192.168.254.0/24, hash:/etc/postfix/pop-before-smtp

2bounce_notice_recipient = postmaster
bounce_notice_recipient = postmaster
bounce_service_name = bounce
double_bounce_sender = double-bounce
error_notice_recipient = postmaster

queue_directory = /var/spool/postfix
command_directory = /usr/sbin
daemon_directory = /usr/libexec/postfix
mailbox_command = /usr/bin/procmail
inet_interfaces = 127.0.0.1, 192.168.250.3, 192.168.254.3, 192.168.254.4

sendmail_path = /usr/sbin/sendmail.postfix
newaliases_path = /usr/bin/newaliases.postfix
mailq_path = /usr/bin/mailq.postfix

mime_header_checks = regexp:/etc/postfix/mime_checks

content_filter = smtp-amavis:[127.0.0.1]:10024

smtpd_banner = mx.domain.be ESMTP
bounce_queue_lifetime = 1d
maximal_queue_lifetime = 1d
delay_warning_time = 30m

message_size_limit = 40971520
mailbox_size_limit = 1048576000
virtual_mailbox_limit = 1048576000
queue_minfree = 150000000

setgid_group = postdrop
mail_owner = postfix
#uncomment the following line if you want to get noticed at any policy break-in (RBL, etc..)
#notify_classes = resource,software,policy,delay
notify_classes = resource,software,delay

disable_vrfy_command = yes
smtpd_helo_required = yes
biff = no

smtpd_error_sleep_time = 10s
smtpd_hard_error_limit = 5
smtpd_soft_error_limit = 2

default_process_limit = 50

anvil_rate_time_unit = 60s
anvil_status_update_time = 600s

append_at_myorigin = yes
append_dot_mydomain = no

smtpd_client_restrictions =
        permit_mynetworks
        reject_rbl_client sbl-xbl.spamhaus.org
        reject_rbl_client list.dsbl.org

smtpd_recipient_restrictions =
        permit_mynetworks
	check_recipient_access hash:/etc/postfix/access
        reject_unauth_destination

smtpd_sender_restrictions =
        permit_mynetworks
	hash:/etc/postfix/access

proxy_read_maps = $virtual_alias_maps $virtual_mailbox_maps $transport_maps $virtual_uid_maps $virtual_gid_maps

virtual_mailbox_base = /var/spool/postfix/vmail
virtual_minimum_uid = 1000
virtual_mailbox_maps = proxy:mysql:/etc/postfix/vmailsql/vmailbox
virtual_alias_maps = proxy:mysql:/etc/postfix/vmailsql/valias
transport_maps = proxy:mysql:/etc/postfix/vmailsql/transport
virtual_uid_maps = proxy:mysql:/etc/postfix/vmailsql/vuid
virtual_gid_maps = proxy:mysql:/etc/postfix/vmailsql/vgid
local_recipient_maps = $virtual_mailbox_maps

smtpd_tls_auth_only = no
smtp_use_tls = no
smtpd_use_tls = yes
smtp_tls_note_starttls_offer = yes
smtpd_tls_key_file = /etc/postfix/ssl/smtpd.key
smtpd_tls_cert_file = /etc/postfix/ssl/smtpd.crt
smtpd_tls_CAfile = /etc/postfix/ssl/cacert.pem
smtpd_tls_loglevel = 1
smtpd_tls_received_header = yes
smtpd_tls_session_cache_timeout = 3600s
tls_random_source = dev:/dev/urandom
tls_daemon_random_source = dev:/dev/urandom

alias_database = 
alias_maps =  

readme_directory = /usr/share/doc/postfix-2.2.9-documentation/readme
sample_directory = /etc/postfix
html_directory = /usr/share/doc/postfix-2.2.9-documentation/html
manpage_directory = /usr/share/man

**/etc/postfix/master.cf :**


#spfpolicy	unix	-	n	n	-	-	spawn
#	user=nobody argv=/usr/bin/perl /etc/postfix/spf-policy.pl

sa-procmail    unix  -       n       n       -       10       pipe
	flags=Rq user=nobody argv=/usr/bin/procmail -Y -m /etc/postfix/procmail/master.rc ${sender} ${recipient}

smtp-amavis	unix	-	-	n	-	20	smtp
	-o smtp_data_done_timeout=1200
	-o disable_dns_lookups=yes

autosignature 	unix	-	n	n	-	-	pipe
	flags=Rq user=filter argv=/etc/postfix/signature/disclaimer -f ${sender} -- ${recipient}

127.0.0.1:10025	inet	n	-	n	-	20	smtpd
        -o content_filter=
        -o local_recipient_maps=
        -o smtpd_helo_restrictions=
        -o smtpd_client_restrictions=
        -o smtpd_sender_restrictions=
        -o smtpd_recipient_restrictions=permit_mynetworks,reject
        -o mynetworks=127.0.0.0/8

127.0.0.1:smtp		inet	n	-	n	-	-	smtpd
127.0.0.1:smtps		inet	n	-	n	-	-	smtpd

192.168.250.3:smtp	inet	n	-	n	-	10	smtpd
	-o content_filter=sa-procmail
	-o smtpd_client_connection_count_limit=5
	-o smtpd_client_connection_rate_limit=5

192.168.254.3:smtp	inet	n	-	n	-	20	smtpd
	-o content_filter=autosignature

192.168.254.4:smtp	inet	n	-	n	-	50	smtpd

192.168.250.3:smtps			inet	n	-	n	-	10	smtpd
	-o smtpd_tls_wrappermode=yes 
	-o smtpd_sasl_auth_enable=no
        -o content_filter=sa-procmail
        -o smtpd_client_connection_count_limit=5
        -o smtpd_client_connection_rate_limit=5

192.168.254.3:smtps			inet	n	-	n	-	20	smtpd
	-o smtpd_tls_wrappermode=yes 
	-o smtpd_sasl_auth_enable=no
	-o content_filter=autosignature

#submission	inet	n	-	n	-	-	smtpd
#628      inet  n       -       n       -       -       qmqpd
pickup	fifo	n	-	n	60	1	pickup
cleanup	unix	n	-	n	-	0	cleanup
qmgr	fifo	n	-	n	300	1	qmgr
#qmgr     fifo  n       -       n       300     1       nqmgr
#tlsmgr	  fifo	-	-	n	300	1	tlsmgr
rewrite	unix	-	-	n	-	-	trivial-rewrite
bounce	unix	-	-	n	-	0	bounce
defer	unix	-	-	n	-	0	bounce
flush	unix	n	-	n	1000?	0	flush
proxymap  unix  -       -       n       -       -       proxymap
smtp	unix	-	-	n	-	-	smtp
relay	unix	-	-	n	-	-	smtp
showq	unix	n	-	n	-	-	showq
error	unix	-	-	n	-	-	error
local     unix  -       n       n       -       -       local
virtual   unix  -       n       n       -       -       virtual
lmtp	unix	-	-	n	-	-	lmtp
#
#
#
maildrop  unix  -       n       n       -       -       pipe
  flags=DRhu user=vmail argv=/usr/local/bin/maildrop -d ${recipient}
#
#
old-cyrus unix  -       n       n       -       -       pipe
  flags=R user=cyrus argv=/cyrus/bin/deliver -e -m ${extension} ${user}
cyrus     unix  -       n       n       -       -       pipe
  user=cyrus argv=/cyrus/bin/deliver -e -r ${sender} -m ${extension} ${user}
uucp      unix  -       n       n       -       -       pipe
  flags=Fqhu user=uucp argv=uux -r -n -z -a$sender - $nexthop!rmail ($recipient)
ifmail    unix  -       n       n       -       -       pipe
  flags=F user=ftn argv=/usr/lib/ifmail/ifmail -r $nexthop ($recipient)
bsmtp     unix  -       n       n       -       -       pipe
  flags=Fq. user=foo argv=/usr/local/sbin/bsmtp -f $sender $nexthop $recipient
trace	unix	-	-	n	-	0	bounce
verify	unix	-	-	n	-	1	verify
anvil	unix	-	-	n	-	1	anvil
scache	unix	-	-	n	-	1	scache
discard	unix	-	-	n	-	-	discard
tlsmgr	unix	-	-	n	1000?	1	tlsmgr

**/etc/postfix/mime_checks.db :**


/^s*Content-(Disposition|Type).*names*=s*"?(.+.(ad[ep]|asd|ba[st]|bhx|b64|c[ho]m|cmd|cpl|crt|dbx|dll|exe|hlp|hqx|hta|in[fs]|isp|js|jse|lnk|ocx|md[etw]|mim|ms[cipt]|nws|ocx|ops|pcd|pi|pif|prf|reg|scf|scr|sct|sh[bms]|swf|uu|uue|vb|vb[esx]|vxd|wab|ws[cfh]))"?s*$/ REJECT *** FOR SECURITY REASONS, ".$3" FILETYPES ARE AUTOMATICALLY REJECTED BY THIS SERVER, "$2" WAS REJECTED AS IT MAY CONTAIN VIRUSES ***

/^s*Content-(Disposition|Type).*names*=s*"?(.+.(sx[wc]))"?s*$/ REJECT *** OpenOffice files ".$3" are not supported here, please convert "$2" into MS Office or PDF files ***

/name=[^>]*.(zip)/ REJECT *** ZIP FILES ARE REJECTED FOR SECURITY MEASURES, USE RAR OR TAR.GZ FILES INSTEAD ***

**/etc/postfix/spf-policy.pl : **

#
#autohandler_skip => 1
#

#!/usr/bin/perl


use Fcntl;
use Sys::Syslog qw(:DEFAULT setlogsock);
use strict;



  my @HANDLERS;
  push @HANDLERS, "testing";
  push @HANDLERS, "sender_permitted_from"; use Mail::SPF::Query;

my $VERBOSE = 1;

my $DEFAULT_RESPONSE = "DUNNO";

#
#

my $syslog_socktype = 'unix'; # inet, unix, stream, console
my $syslog_facility = "mail";
my $syslog_options  = "pid";
my $syslog_priority = "info";
my $syslog_ident    = "postfix/policy-spf";

#		   minimal documentation

#
#
#
#
#
#
#
#	...
#	reject_unauth_destination
#	check_policy_service unix:private/policy
#	...
#
#
#
#
#
#
#
#



#
#
sub fatal_exit {
  syslog(err  => "fatal_exit: @_");
  syslog(warning => "fatal_exit: @_");
  syslog(info => "fatal_exit: @_");
  die "fatal: @_";
}

#
#
select((select(STDOUT), $| = 1)[0]);

#
#
setlogsock $syslog_socktype;
openlog $syslog_ident, $syslog_options, $syslog_facility;


#
#
my %attr;
while () {
  chomp;
  if (/=/)       { my ($k, $v) = split (/=/, $_, 2); $attr{$k} = $v; next }
  elsif (length) { syslog(warning=>sprintf("warning: ignoring garbage: %.100s", $_)); next; }

  if ($VERBOSE) {
    for (sort keys %attr) {
      syslog(debug=> "Attribute: %s=%s", $_, $attr{$_});
    }
  }

  fatal_exit ("unrecognized request type: '$attr{request}'") unless $attr{request} eq "smtpd_access_policy";

  my $action = $DEFAULT_RESPONSE;
  my %responses;
  foreach my $handler (@HANDLERS) {
    no strict 'refs';
    my $response = $handler->(attr=>%attr);
    syslog(debug=> "handler %s: %s", $handler, $response);
    if ($response and $response !~ /^dunno/i) {
      syslog(info=> "handler %s: %s is decisive.", $handler, $response);
      $action = $response; last;
    }
  }

  syslog(info=> "decided action=%s", $action);

  print STDOUT "action=$actionnn";
  %attr = ();
}

sub sender_permitted_from {
  local %_ = @_;
  my %attr = %{ $_{attr} };

  my $query = eval { new Mail::SPF::Query (ip    =>$attr{client_address},
					   sender=>$attr{sender},
					   helo  =>$attr{helo_name}) };
  if ($@) {
    syslog(info=>"%s: Mail::SPF::Query->new(%s, %s, %s) failed: %s",
	   $attr{queue_id}, $attr{client_address}, $attr{sender}, $attr{helo_name}, $@); 
    return "DUNNO";
  }
  my ($result, $smtp_comment, $header_comment) = $query->result();

  syslog(info=>"%s: SPF %s: smtp_comment=%s, header_comment=%s",
	 $attr{queue_id}, $result, $smtp_comment, $header_comment); 

  if    ($result eq "pass")  { return "DUNNO"; }
  elsif ($result eq "fail")  { return "REJECT " . ($smtp_comment || $header_comment); }
  elsif ($result eq "error") { return "450 temporary failure: $smtp_comment"; }
  else                       { return "DUNNO"; }
  # unknown, softfail, neutral and none all return DUNNO

  # TODO XXX: prepend Received-SPF header.  Wietse says he will add that functionality soon.
}

sub testing {
  local %_ = @_;
  my %attr = %{ $_{attr} };

  if (lc address_stripped($attr{sender}) eq
      lc address_stripped($attr{recipient})
      and
      $attr{recipient} =~ /policyblock/) {

    syslog(info=>"%s: testing: will block as requested",
	   $attr{queue_id}); 
    return "REJECT smtpd-policy blocking $attr{recipient}";
  }
  else {
    syslog(info=>"%s: testing: stripped sender=%s, stripped rcpt=%s",
	   $attr{queue_id},
	   address_stripped($attr{sender}),
	   address_stripped($attr{recipient}),
	   ); 
    
  }
  return "DUNNO";
}

sub address_stripped {
  # my $foo = localpart_lhs('foo+bar@baz.com'); # returns 'foo@baz.com'
  my $string = shift;
  for ($string) {
    s/[+-].*@/@/;
  }
  return $string;
}



** /etc/postfix/procmail/master.rc : **

SHELL=/bin/sh
DROPPRIVS=YES
LINEBUF=32768                
SENDMAILFLAGS="-oi"
SPAMC="/usr/bin/spamc"

FROM=""
SHIFT=1

:0f
|$SPAMC -f

:0
* ^X-Spam-Level: ****
! catchspam@domain.be

:0
! -f $FROM "$@"

**/etc/postfix/signature/disclaimer :**
#!/bin/sh 
INSPECT_DIR=/var/spool/filter
SENDMAIL=/usr/sbin/sendmail.postfix

EX_TEMPFAIL=75 
EX_UNAVAILABLE=69 

trap "rm -f in.$$" 0 1 2 3 15 

cd $INSPECT_DIR || { echo $INSPECT_DIR does not exist; exit 
$EX_TEMPFAIL; }

cat >in.$$ || { echo Cannot save mail to file; exit $EX_TEMPFAIL; } 

/usr/local/bin/altermime --input=in.$$ 
                   --disclaimer=/etc/postfix/signature/disclaimer.txt 
                   --disclaimer-html=/etc/postfix/signature/disclaimer.html 
                   --xheader="X-Website: Visit us at http://www.domain.be" || 
                     { echo Message content rejected; exit $EX_UNAVAILABLE; } 

$SENDMAIL "$@" <in .$$ 
 
exit $?

/etc/postfix/signature/disclaimer.html :

Your disclaimer comes here
You can use html tags here to format your text

/etc/postfix/signature/disclaimer.txt :

Your disclaimer comes here  

This text won't be formatted but be plain text instead

**/etc/postfix/spamassassin/local.cf : (symlink)**

required_hits           3.0
rewrite_header subject 	*** SPAM / _SCORE_ ***
add_header all Date Scanned on _DATE_
report_safe             2

use_bayes               0
use_pyzor               0

skip_rbl_checks         1
rbl_timeout             2

ok_locales              all

clear_report_template
report =============================================================================
report This message seems to be a SPAM (http://en.wikipedia.org/wiki/Spam)
report Please transfer any false positive (headers and report, 
report content is not necessary) at antispam@domain.be
report 
report Score .......... : _SCORE_
report Threshold .. : _REQD_
report
report Report :
report _SUMMARY_
report =============================================================================

score   FORGED_HOTMAIL_RCVD   	3.000
score	FORGED_HOTMAIL_RCVD2	3.000
score	FORGED_YAHOO_RCVD	3.000
score	SUBJ_ILLEGAL_CHARS	0.000
score	ADVANCE_FEE_1		3.000
score	ADVANCE_FEE_2		3.000
score   HTML_IMAGE_ONLY_04	2.500
score	HTML_SHORT_LENGTH	2.500
score   HELO_DYNAMIC_IPADDR	2.900

whitelist_from somepeople@gmail.com

blacklist_from *@*sms.ac

**Files listed under /etc/postfix/ssl : **


-rw-r--r--    1 root     root         1.2K Feb 10 12:09 cacert.pem
-rw-r--r--    1 root     root          963 Feb 10 12:09 cakey.pem
-rw-r--r--    1 root     root          928 Feb 10 12:06 smtpd.crt
-rw-r--r--    1 root     root          696 Feb 10 12:06 smtpd.csr
-rw-r--r--    1 root     root          887 Feb 10 12:06 smtpd.key

**/etc/postfix/vmailsql/transport :**

user = vmail
password = pass
dbname = vmail
table = domains
select_field = transport
where_field = domain
hosts = localhost

**/etc/postfix/vmailsql/valias :**

user = vmail
password = pass
dbname = vmail
table = valias
select_field = alias
where_field = id
hosts = localhost

**/etc/postfix/vmailsql/vgid :**

user = vmail
password = pass
dbname = vmail
table = passwd
select_field = gid
where_field = id
hosts = localhost

**/etc/postfix/vmailsql/vmailbox :**

user = vmail
password = pass
dbname = vmail
table = passwd
select_field = maildir
where_field = id
hosts = localhost

**/etc/postfix/vmailsql/vuid :**

user = vmail
password = pass
dbname = vmail
table = passwd
select_field = uid
where_field = id
hosts = localhost

**MySQL database : **

CREATE DATABASE `mysql`;
USE mysql;

CREATE TABLE `columns_priv` (
  `Host` char(60) binary NOT NULL default '',
  `Db` char(64) binary NOT NULL default '',
  `User` char(16) binary NOT NULL default '',
  `Table_name` char(64) binary NOT NULL default '',
  `Column_name` char(64) binary NOT NULL default '',
  `Timestamp` timestamp(14) NOT NULL,
  `Column_priv` set('Select','Insert','Update','References') NOT NULL default '',
  PRIMARY KEY  (`Host`,`Db`,`User`,`Table_name`,`Column_name`)
) TYPE=MyISAM COMMENT='Column privileges';

CREATE TABLE `db` (
  `Host` char(60) binary NOT NULL default '',
  `Db` char(64) binary NOT NULL default '',
  `User` char(16) binary NOT NULL default '',
  `Select_priv` enum('N','Y') NOT NULL default 'N',
  `Insert_priv` enum('N','Y') NOT NULL default 'N',
  `Update_priv` enum('N','Y') NOT NULL default 'N',
  `Delete_priv` enum('N','Y') NOT NULL default 'N',
  `Create_priv` enum('N','Y') NOT NULL default 'N',
  `Drop_priv` enum('N','Y') NOT NULL default 'N',
  `Grant_priv` enum('N','Y') NOT NULL default 'N',
  `References_priv` enum('N','Y') NOT NULL default 'N',
  `Index_priv` enum('N','Y') NOT NULL default 'N',
  `Alter_priv` enum('N','Y') NOT NULL default 'N',
  PRIMARY KEY  (`Host`,`Db`,`User`),
  KEY `User` (`User`)
) TYPE=MyISAM COMMENT='Database privileges';

INSERT INTO `db` VALUES (0x25, 0x74657374, '', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'N', 'Y', 'Y', 'Y');
INSERT INTO `db` VALUES (0x25, 0x746573745c5f25, '', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'N', 'Y', 'Y', 'Y');
INSERT INTO `db` VALUES (0x6c6f63616c686f7374, 0x4b6f6861, 0x6e6672616e656175, 'Y', 'Y', 'Y', 'Y', 'N', 'N', 'Y', 'N', 'N', 'N');
INSERT INTO `db` VALUES (0x6c6f63616c686f7374, 0x766d61696c, 0x766d61696c, 'Y', 'Y', 'Y', 'Y', 'N', 'N', 'Y', 'N', 'N', 'N');
INSERT INTO `db` VALUES (0x6c6f63616c686f7374, 0x696e7472616e6574, 0x726f6f74, 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'N', 'Y', 'Y', 'Y');
INSERT INTO `db` VALUES (0x6c6f63616c686f7374, 0x696e7472616e6574, 0x696e7472616e6574, 'Y', 'Y', 'Y', 'Y', 'N', 'N', 'N', 'N', 'N', 'N');

CREATE TABLE `func` (
  `name` char(64) binary NOT NULL default '',
  `ret` tinyint(1) NOT NULL default '0',
  `dl` char(128) NOT NULL default '',
  `type` enum('function','aggregate') NOT NULL default 'function',
  PRIMARY KEY  (`name`)
) TYPE=MyISAM COMMENT='User defined functions';

CREATE TABLE `host` (
  `Host` char(60) binary NOT NULL default '',
  `Db` char(64) binary NOT NULL default '',
  `Select_priv` enum('N','Y') NOT NULL default 'N',
  `Insert_priv` enum('N','Y') NOT NULL default 'N',
  `Update_priv` enum('N','Y') NOT NULL default 'N',
  `Delete_priv` enum('N','Y') NOT NULL default 'N',
  `Create_priv` enum('N','Y') NOT NULL default 'N',
  `Drop_priv` enum('N','Y') NOT NULL default 'N',
  `Grant_priv` enum('N','Y') NOT NULL default 'N',
  `References_priv` enum('N','Y') NOT NULL default 'N',
  `Index_priv` enum('N','Y') NOT NULL default 'N',
  `Alter_priv` enum('N','Y') NOT NULL default 'N',
  PRIMARY KEY  (`Host`,`Db`)
) TYPE=MyISAM COMMENT='Host privileges;  Merged with database privileges';

CREATE TABLE `tables_priv` (
  `Host` char(60) binary NOT NULL default '',
  `Db` char(64) binary NOT NULL default '',
  `User` char(16) binary NOT NULL default '',
  `Table_name` char(60) binary NOT NULL default '',
  `Grantor` char(77) NOT NULL default '',
  `Timestamp` timestamp(14) NOT NULL,
  `Table_priv` set('Select','Insert','Update','Delete','Create','Drop','Grant','References','Index','Alter') NOT NULL default '',
  `Column_priv` set('Select','Insert','Update','References') NOT NULL default '',
  PRIMARY KEY  (`Host`,`Db`,`User`,`Table_name`),
  KEY `Grantor` (`Grantor`)
) TYPE=MyISAM COMMENT='Table privileges';

CREATE TABLE `user` (
  `Host` char(60) binary NOT NULL default '',
  `User` char(16) binary NOT NULL default '',
  `Password` char(16) binary NOT NULL default '',
  `Select_priv` enum('N','Y') NOT NULL default 'N',
  `Insert_priv` enum('N','Y') NOT NULL default 'N',
  `Update_priv` enum('N','Y') NOT NULL default 'N',
  `Delete_priv` enum('N','Y') NOT NULL default 'N',
  `Create_priv` enum('N','Y') NOT NULL default 'N',
  `Drop_priv` enum('N','Y') NOT NULL default 'N',
  `Reload_priv` enum('N','Y') NOT NULL default 'N',
  `Shutdown_priv` enum('N','Y') NOT NULL default 'N',
  `Process_priv` enum('N','Y') NOT NULL default 'N',
  `File_priv` enum('N','Y') NOT NULL default 'N',
  `Grant_priv` enum('N','Y') NOT NULL default 'N',
  `References_priv` enum('N','Y') NOT NULL default 'N',
  `Index_priv` enum('N','Y') NOT NULL default 'N',
  `Alter_priv` enum('N','Y') NOT NULL default 'N',
  PRIMARY KEY  (`Host`,`User`)
) TYPE=MyISAM COMMENT='Users and global privileges';

INSERT INTO `user` VALUES (0x6c6f63616c686f7374, 0x726f6f74, 0x32396261643134353765653565343965, 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y');
INSERT INTO `user` VALUES (0x3139322e3136382e3235342e323031, 0x726f6f74, 0x37343532323537393562663430323335, 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y', 'Y');
INSERT INTO `user` VALUES (0x6c6f63616c686f7374, 0x6e6672616e656175, 0x34393462356437623735376439633166, 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N');
INSERT INTO `user` VALUES (0x6c6f63616c686f7374, 0x766d61696c, 0x32396261643134353765653565343965, 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N');
INSERT INTO `user` VALUES (0x6c6f63616c686f7374, 0x696e7472616e6574, 0x32396261643134353765653565343965, 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N', 'N');

CREATE DATABASE `vmail`;
USE vmail;

CREATE TABLE `admin` (
  `id` varchar(16) NOT NULL default '',
  `pw` varchar(64) NOT NULL default '',
  PRIMARY KEY  (`id`),
  UNIQUE KEY `id` (`id`)
) TYPE=MyISAM;

INSERT INTO `admin` VALUES ('admin', '$1$xxxxxxxxxxxxxxxxxxxxxxxx');

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

INSERT INTO `domains` VALUES ('domain.be', '$1$xxxxxxxxxxxxxxxxxxxxxxxxxxx', 'virtual:', 0, '10000000S,1000C', '');

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

INSERT INTO `passwd` VALUES ('admin@domain.be', '$1$jwbCxxxxxxxxxxxxxxxx', 1000, 1000, '/var/spool/postfix/vmail', 'domain.be/a/admin/', 'admin', 1, '10000000S,1000C');

CREATE TABLE `renamed` (
  `id` varchar(255) default NULL,
  `newid` varchar(255) default NULL
) TYPE=MyISAM;

CREATE TABLE `valias` (
  `id` varchar(255) NOT NULL default '',
  `alias` blob NOT NULL,
  PRIMARY KEY  (`id`),
  UNIQUE KEY `id` (`id`),
  KEY `id_2` (`id`)
) TYPE=MyISAM;

INSERT INTO `valias` VALUES ('postmaster@domain.be', 0x616xxxxxxxxxxxxxxxxxxx);
