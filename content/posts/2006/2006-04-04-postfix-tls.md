---
date: 2006-04-04
title: "Postfix + TLS"
---

This document describes how to install a mail server based on postfix that is capable of TLS. I tested it on CentOS 3.6 and 4.2.



**I'm not sure which packages are actually needed for Postfix using TLS.. these are the ones installed on my system :**
`[root@server](1013)# rpm -qa | grep -i sasl
cyrus-sasl-gssapi-2.1.15-10
cyrus-sasl-plain-2.1.15-10
cyrus-sasl-2.1.15-10
cyrus-sasl-md5-2.1.15-10
cyrus-sasl-devel-2.1.15-10`

**Generate your SSL key and pem (based on http://www.projektfarm.com/en/support/howto/postfix_smtp_auth_tls.html):**

    
    <code>openssl genrsa -des3 -rand /etc/hosts -out smtpd.key 1024
    -> enter a password for smtpd.key
    
    chmod 600 smtpd.key
    
    openssl req -new -key smtpd.key -out smtpd.csr
    
    -> Enter your password for smtpd.key.
    ---> Enter your Country Name (e.g., "BE").
    ---> Enter your State or Province Name.
    ---> Enter your City.
    ---> Enter your Organization Name (e.g., the name of your company).
    ---> Enter your Organizational Unit Name (e.g. "IT Department").
    ---> Enter the Fully Qualified Domain Name of the system (e.g. "server1.example.com").
    ---> Enter your Email Address.
    
    Don't fill the following :
    
    ---> Enter a challenge password.
    ---> Enter an optional company name.
    
    openssl x509 -req -days 3650 -in smtpd.csr -signkey smtpd.key -out smtpd.crt
    
    -> Again, enter your password for smtpd.key.
    
    openssl rsa -in smtpd.key -out smtpd.key.unencrypted
    
    -> Again, enter your password for smtpd.key.
    
    mv -f smtpd.key.unencrypted smtpd.key
    openssl req -new -x509 -extensions v3_ca -keyout cakey.pem -out cacert.pem -days 3650
    
    -> Again, enter your password for smtpd.key.
     Enter your Country Name (e.g., "DE").
    ---> Enter your State or Province Name.
    ---> Enter your City.
    ---> Enter your Organization Name (e.g., the name of your company).
    ---> Enter your Organizational Unit Name (e.g. "IT Department").
    ---> Enter the Fully Qualified Domain Name of the system (e.g. "server1.example.com").
    ---> Enter your Email Address.</code>



**Edit /etc/postfix/main.cf and add :**
`smtpd_tls_auth_only = no
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
tls_daemon_random_source = dev:/dev/urandom`

**Edit /etc/postfix/master.cf :**
`smtps                   inet    n       -       n       -       -       smtpd
        -o smtpd_tls_wrappermode=yes
        -o smtpd_sasl_auth_enable=no`

If you want SMTP auth, you'll need smtpd_sasl_auth_enable=yes

**Open port tcp/465 under iptables**

**service postfix restart**

**Check if TLS is working (the 250-STARTTLS line) :**
`[root@server](1025)# telnet localhost 25
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 mx.domain.be ESMTP
ehlo mx.domain.be
250-mx.domain.be
250-PIPELINING
250-SIZE 40971520
250-ETRN
**250-STARTTLS**
250 8BITMIME`
