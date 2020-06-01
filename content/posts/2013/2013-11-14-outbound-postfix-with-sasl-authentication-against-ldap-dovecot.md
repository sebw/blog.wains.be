---
date: 2013-11-14
title: "Outbound Postfix with SASL Authentication against LDAP (Dovecot)"
---







categories:
- Postfix


I recently had to set up an outbound Postfix server with SASL authentication against LDAP.

I'm a huge fan of Dovecot, so I did go with it instead of Cyrus which was a pain to set up a few years back. Not sure about now.

I hadn't done that in a while, and if you look up on this site, you'll see I actually did SASL auth against MySQL, a couple of years ago. Since then, Dovecot reached v2, which involves a lot of changes in the configuration files.

Tested on RHEL 6.3 + Postfix 2.6.6 + Dovecot 2.0.9 + Thunderbird 17 and Outlook 2003/7/10

I assume you install Postfix and Dovecot with yum from the RHEL repositories.

Just add the configuration in the configuration files and you should be fine. Tune to your liking. Some settings may not be the most sensible for your environment, so please do not blindly copy-paste and be done with it.

**/etc/postfix/master.conf**


    
    
    # SASL Auth
    mynetworks = 127.0.0.0/8
    smtpd_sasl_auth_enable = yes
    broken_sasl_auth_clients = yes
    smtpd_sasl_type = dovecot
    smtpd_sasl_path = private/auth
    smtpd_sasl_security_options = noanonymous
    smtpd_recipient_restrictions = permit_mynetworks, permit_sasl_authenticated, reject_unauth_destination
    
    # Display the auth user in the headers
    smtpd_sasl_authenticated_header = yes
    
    # Next hop
    relayhost=[smtp.example.org]:25
    
    # Postfix TLS
    smtpd_tls_auth_only = no
    smtp_use_tls = no
    smtpd_use_tls = no
    smtp_tls_note_starttls_offer = no
    smtpd_tls_cert_file = /etc/ssl/certs/mycert.pem
    smtpd_tls_key_file = /etc/ssl/certs/mycert.pem
    smtpd_tls_loglevel = 1
    smtpd_tls_received_header = yes
    smtpd_tls_session_cache_database = btree:/var/lib/postfix/smtpd_tls_session_cache
    smtpd_tls_session_cache_timeout = 3600s
    tls_random_source = dev:/dev/urandom
    




**/etc/postfix/master.conf**


    
    
    smtps    inet    n    -    n    -    10    smtpd
        -o smtpd_sasl_auth_enable=yes
            -o smtpd_tls_wrappermode=yes
            -o smtpd_client_connection_count_limit=5
            -o smtpd_client_connection_rate_limit=5
    



**/etc/dovecot/dovecot.conf**


    
    
    auth_mechanisms = plain login
    disable_plaintext_auth = no
    
    # I'm only using Dovecot for SASL auth, not POP or IMAP
    protocols = none
    
    ssl = no
    
    # Debug
    auth_debug=no
    auth_verbose=no
    auth_debug_passwords=no
    mail_debug=yes
    verbose_ssl=no
    auth_verbose_passwords=no
    
    # Postfix SASL auth
    service auth {
      unix_listener /var/spool/postfix/private/auth {
        group = postfix
        mode = 0660
        user = postfix
      }
      user = root
    }
    
    passdb {
      driver = ldap
      args = /etc/dovecot/ldap.conf.ext
    }
    



I owe you a bit of explanation here though.

I'm using the auth_bind method. It means you check if the user is valid by making a bind with the provided credentials. This is nice, you don't need to have access to the password string in LDAP (which I didn't have), and deal with the hashing method used.

Also, something specific to my environment: users would configure their client with their username, which in ldap is under "companyLoginName". Somehow, Dovecot needs to test against the CN, so you have to lookup the companyLoginName string in order to retrieve the cn, you can do so with pass_filter. Then you assign the resulting CN as the actual username (see: pass_attrs).

**/etc/dovecot/ldap.conf.ext**


    
    
    hosts = ldap.example.org
    dn = cn=some,ou=string,ou=from,o=ldap
    dnpass = password
    
    debug_level = 0
    
    auth_bind = yes
    base = ou=somewhere,o=ldap
    scope = subtree
    
    pass_attrs = cn=user
    pass_filter = (&(objectClass=companyAccount)(companyLoginName=%u))
    



