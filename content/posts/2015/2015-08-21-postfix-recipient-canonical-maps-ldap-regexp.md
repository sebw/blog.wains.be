---
date: 2015-08-21
title: "Postfix routing emails using LDAP or regexp with different smptd processes"
---
If you want to redirect emails you can use the `recipient_canonical_maps` option.

This `cleanup` option is usually specified globally in `main.cf`.

If you want to treat different domains with different filtering techniques, you can do this in `master.cf` instead:

```
0.0.0.0:2525       inet  n       -       n       -       -       smtpd
        -o cleanup_service_name=cleanup-2525
        -o myhostname=mxrouting.ldap.example.org
        -o smtpd_banner=$myhostname

cleanup-2525 unix  n       -       n       -       0       cleanup
        -o recipient_canonical_maps=ldap:/etc/postfix/routing-ldap.example.org

0.0.0.0:2526       inet  n       -       n       -       -       smtpd
        -o cleanup_service_name=cleanup-2526
        -o myhostname=mxrouting.regexp.example.org
        -o smtpd_banner=$myhostname

cleanup-2526 unix  n       -       n       -       0       cleanup
        -o recipient_canonical_maps=regexp:/etc/postfix/routing-regexp.example.org
```

This means that Postfix will listen on port `TCP/2525` for emails to @ldap.example.org, and apply an LDAP query to know where to route the email.

On port `TCP/2526`, emails for @regexp.example.org will be redirected based on regexp rules.

Examples or recipient_canonical_maps configurations:

`/etc/postfix/routing-ldap.example.org`:

```
server_host = 192.168.0.1
search_base = ou=company,ou=name,o=prd
bind_dn = cn=blah,ou=TechObjects,o=SYSTEM
bind_pw = MyVerySecurePassword

query_filter = (&(mail=%s))
result_attribute = mailstop
result_format = %U@%s.%D
```

`/etc/postfix/routing-regexp.example.org`:

```
/^support(.*@)regexp.example.org$/ ec${1}server1.regexp.example.org
/^business(.*@)regexp.example.org$/ ms${1}server2.regexp.example.org
```
