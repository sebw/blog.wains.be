---
date: 2005-09-15
title: "How to fix the Mail--SPF--Query error after upgrading to SpamAssassin 3.1.0"
---

A new version of SpamAssassin has been released lately..
Upgrade from version 3.0.4 to 3.1.0..

The upgrade led to an error :
`Sep 15 17:21:44 xxx spamd 13300 : Can't locate Mail/SPF/Query.pm in @INC (@INC contains: ../lib /usr/lib/perl5/site_perl/5.8.0/i386-linux-thread-multi /usr/lib/perl5/site_perl/5.8.0 /usr/lib/perl5/5.8.0/i386-linux-thread-multi /usr/lib/perl5/5.8.0 /usr/lib/perl5/site_perl /usr/lib/perl5/vendor_perl/5.8.0/i386-linux-thread-multi /usr/lib/perl5/vendor_perl/5.8.0 /usr/lib/perl5/vendor_perl) at /usr/lib/perl5/site_perl/5.8.0/Mail/SpamAssassin/Plugin/SPF.pm line 272, <GEN101> line 1545.`

After a few researches, I have figured out the perl library Mail::SPF::Query was needed
This new version of SpamAssassin apparently has SPF enabled by default now...

How to install the missing library :
`1. perl -MCPAN -e shell
1. Under CPAN : install Mail::SPF::Query
2. Quit CPAN after installation of the library
3. service spamassassin reload`

It should work now !
