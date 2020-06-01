---
date: 2006-12-29
title: "Install FuzzyOCR for SpamAssassin on CentOS/RHEL"
---







categories:
- Howto
- Linux
- Security


Tested under CentOS 3.8 and CentOS 4.4, both running SpamAssassin 3.1.7 built from srpm

**wget http://users.own-hero.net/~decoder/fuzzyocr/fuzzyocr-latest.tar.gz**

**Ungzip : **
`gzip -d fuzzyocr-latest.tar.gz`

**Untar : **
`tar xvf fuzzyocr-latest.tar`



**Packages needed and found in the CentOS repositories :**
`yum install netpbm netpbm-progs ImageMagick libungif libungif-progs`

**Packages needed and found in SecurityTeamUS repository :**
First, you need to install that SecurityTeamUS repo :
`rpm -ihv http://repo.securityteam.us/repository/redhat/securityteamus-repo-latest.rpm`

Then :
`yum install perl-Digest-MD5`

**Packages needed and found in Dag's repo (install dag repository : http://dag.wieers.com/) : **
`yum install gocr 
yum install perl-String-Approx`

**Copy plugin to spamassassin's plugin directory :**
Under CentOS/RHEL 3 :
`cp FuzzyOcr.pm /usr/lib/perl5/site_perl/5.8.0/Mail/SpamAssassin/Plugin/`

Under CentOS/RHEL 4 :
`cp FuzzyOcr.pm /usr/lib/perl5/site_perl/5.8.5/Mail/SpamAssassin/Plugin/`

Find out the plugin path : 
`rpm -ql perl-Mail-SpamAssassin | grep -i plugin | grep -i perl`

**Copy FuzzyOCR config files to spamassassin config directory :**
`cp FuzzyOcr.cf /etc/mail/spamassassin/
cp FuzzyOcr.words.sample /etc/mail/spamassassin/FuzzyOcr.words`

**Make sure SA will call the plugin :**
Under CentOS/RHEL 3 :
`echo "loadplugin FuzzyOcr /usr/lib/perl5/site_perl/5.8.0/Mail/SpamAssassin/Plugin/FuzzyOcr.pm" >> /etc/mail/spamassassin/v310.pre`

Under CentOS/RHEL 4 :
`echo "loadplugin FuzzyOcr /usr/lib/perl5/site_perl/5.8.5/Mail/SpamAssassin/Plugin/FuzzyOcr.pm" >> /etc/mail/spamassassin/v310.pre`

**Edit /etc/mail/spamassassin/FuzzyOcr.cf :**
Comment the loadplugin line we moved to v310.pre in the previous step
#loadplugin FuzzyOcr FuzzyOcr.pm

Change the log file location :
#focr_logfile /etc/mail/spamassassin/FuzzyOcr.log
focr_logfile /var/log/fuzzyocr.log

**Create the log file and set rotation :**
`touch /var/log/fuzzyocr.log
chown spamd:spamd /var/log/fuzzyocr.log (or whatever user running SpamAsssassin)`

Create /etc/logrotate.d/fuzzyocr :
`/var/log/fuzzyocr.log {
     rotate 5
     weekly
     compress
     delaycompress
     create 644 spamd spamd
}`

**As root, run "spamassassin --lint" (double dash lint), it should not return any output unless there's something wrong**

**Test FuzzyOCR :**
spamassassin -t samples/png.eml 

Output (snippet) :
Content analysis details:   (46.9 points, 3.0 required)

` pts rule name              description
---- ---------------------- --------------------------------------------------
 0.8 EXTRA_MPART_TYPE       Header has extraneous Content-type:...type= entry
 2.0 DATE_IN_FUTURE_03_06   Date: is 3 to 6 hours after Received: date
 0.1 TW_QU                  BODY: Odd Letter Triples with QU
 0.0 HTML_MESSAGE           BODY: HTML included in message
 0.7 MY_CID_AND_STYLE       SARE cid and style
 3.0 LONGWORDS              Long string of long words
 3.4 FORGED_MUA_OUTLOOK     Forged mail pretending to be from MS Outlook
  37 FUZZY_OCR              BODY: Mail contains an image with common spam text inside
                            Words found:
                            "alert" in 3 lines
                            "news" in 4 lines
                            "symbol" in 1 lines
                            "alert" in 3 lines
                            "stock" in 1 lines
                            "investor" in 4 lines
                            "company" in 2 lines
                            "buy" in 1 lines
                            "price" in 3 lines
                            "trade" in 2 lines
                            "target" in 3 lines
                            "banking" in 1 lines
                            "service" in 3 lines
                            "recommendation" in 1 lines
                            "levitra" in 1 lines
                            "software" in 2 lines
                            (35 word occurrences found)`

It works !

`On my system (Pentium 3 @ 1 Ghz, 768 Mb), I raised the value of "focr_timeout" in the config file to 30.
It depends on your system, the default value is 10.
While testing the setup on sample files, fuzzyocr was reaching the timeout value on animated-gif.eml`

**First spam caught :**

Dec 29 17:15:01 box spamd[8776]: spamd: identified spam (12.8/3.0) for spamd:102 in 7.4 seconds, 25504 bytes. 
Dec 29 17:15:01 box spamd[8776]: spamd: result: Y 12 - FUZZY_OCR,HTML_10_20,HTML_IMAGE_ONLY_28,HTML_MESSAGE,MIME_HTML_ONLY,SARE_GIF_ATTACH,SARE_GIF_STOX,SUBJ_ALL_CAPS,UPPERCASE_75_100 scantime=7.4,size=25504,user=spamd,uid=102,required_score=3.0,rhost=localhost,raddr=127.0.0.1,rport=/var/run/spamd.sock,mid=<4594960B.7040208@wecoshipping.com>,autolearn=disabled

More info : 
[http://wiki.apache.org/spamassassin/FuzzyOcrPlugin](http://wiki.apache.org/spamassassin/FuzzyOcrPlugin)
[http://fuzzyocr.own-hero.net/wiki/Downloads](http://fuzzyocr.own-hero.net/wiki/Downloads)
