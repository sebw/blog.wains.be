---
date: 2015-04-29
title: "Postfix routing and rewriting of addresses based on LDAP attributes"
---
Date: 2015-04-29

Note: This has been tested on RHEL6 and Postfix 2.6.6 from RHEL repositories and version 2.10 from postfix.org. RHEL6 version contains a [nasty bug](https://bugzilla.redhat.com/show_bug.cgi?id=1227761), I recommend you use Postfix.org RPMS.

We want to route emails thanks to rewriting capabilities of Postfix.

An LDAP directory will provide us with an attribute.

We will use the "mailstop" attribute here. Different values can be defined: "brussels" or "stockholm".

An email would be relayed through this Postfix instance.

Examples:

```
Attribute = "brussels": john.doe@example.org would get rewritten john.doe@brussels.example.org
Attribute = "stockholm": john.doe@example.org would get rewritten john.doe@stockholm.example.org
No attribute or email address not present in LDAP : no rewriting
```

The LDAP configuration would be:

```
server_host = ldap.example.org
search_base = ou=some,ou=population,o=directory
query_filter = (&(mail=%s))
result_attribute = mailstop
result_format = %U@%s.%D
bind_dn = cn=login,ou=TechObjects,o=SYSTEM
bind_pw = password
```

We are taking the email address as input (%s). The value returned as output would be stored in the %s variable in result_format. We would construct the rewriten email address with the %U and %D variables (see [postfix doc for details](http://www.postfix.org/ldap_table.5.html))

From now on, we can query the LDAP server and expect something like this:

```
john.doe@brussels.example.org
```

Now, we are going to configure Postfix to actually rewrite addresses, using [recipient_canonical_maps](http://www.postfix.org/postconf.5.html#recipient_canonical_maps) which is an option of the cleanup process.

```
recipient_canonical_maps = ldap:/etc/postfix/ldap.cf
```

Then send an email to john.doe@example.org through that relay:

```
Apr 29 12:55:17 mx.intra.example.org postfix/smtpd[20276]: disconnect from mxin.example.org[192.168.96.200]
Apr 29 12:55:17 mx.intra.example.org postfix/smtp[20280]: 3A59580370: to=<john.doe@brussels.example.org>, orig_to=<john.doe@example.org>, relay=mx.brussels.example.org[192.168.96.4]:25, delay=0.08, delays=0.05/0.01/0.01/0.02, dsn=2.0.0, status=sent (250 Message queued)
```

We can see the address has been rewritten.

If the LDAP server is down, emails will be waiting in the maildrop queue.

If the LDAP gives several replies, Postfix will rewrite to the first result.
