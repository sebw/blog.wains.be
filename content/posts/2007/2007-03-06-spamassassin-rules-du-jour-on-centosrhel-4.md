---
date: 2007-03-06
title: "SpamAssassin - Rules du jour on CentOS/RHEL 4"
---







categories:
- Howto
- Linux
- Red Hat/CentOS


**Download the script (functional for me as of today with SpamAssassin 3.1.8) :**
`wget http://blog.wains.be/pub/rules_du_jour.gz -O /usr/local/bin/rules_du_jour.gz && gzip -d /usr/local/bin/rules_du_jour.gz`

I always find it difficult to download the script (site down, etc), so I put my current script online.



**Set the exec bit :**
`chmod +x /usr/local/bin/rules_du_jour`

**Create the config file :**
mkdir /etc/rulesdujour
Create and edit /etc/rulesdujour/config :

`TRUSTED_RULESETS="SARE_ADULT SARE_STOCKS SARE_WHITELIST SARE_RANDOM SARE_EVILNUMBERS0 SARE_BML TRIPWIRE"
SA_DIR=/etc/mail/spamassassin
EMAIL_RDJ_UPDATE_ONLY=
SINGLE_EMAIL_ONLY=true
MAIL_ADDRESS=your@address.com
SA_LINT="/usr/bin/spamassassin --lint"
SA_RESTART="/etc/rc.d/init.d/spamd restart"
TMPDIR="${SA_DIR}/RulesDuJour"`

**Set up a crontab :**
`42 00 * * * (/usr/local/bin/./rules_du_jour)`

Anytime the rules are updated, you get a notification email.
