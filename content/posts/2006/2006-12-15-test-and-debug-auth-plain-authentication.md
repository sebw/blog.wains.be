---
date: 2006-12-15
title: "Test and debug AUTH PLAIN authentication"
---







categories:
- Howto
- Linux
- Postfix
- Security


This command will give you the string that should be passed at AUTH PLAIN

For test@domain.be, you'd type this :

`perl -MMIME::Base64 -e 'print encode_base64("00test@domain.be00password")'`



Where "test@domain.be" is your username and "password" is.. your password
Your username and password should start with the string "00"

The output is : AHRlc3RAZG9tYWluLmJlAHBhc3N3b3Jk

Now, you can test the authentication..

`user@host:~$ telnet mx.domain.be 25
Trying 10.0.0.1...
Connected to mx.domain.be.
Escape character is '^]'.
220 mx.domain.be ESMTP
EHLO mx.domain.be
250-mx.domain.be
250-PIPELINING
250-SIZE 10971520
250-ETRN
250-AUTH PLAIN LOGIN
250-AUTH=PLAIN LOGIN
250 8BITMIME
AUTH PLAIN AHRlc3RAZG9tYWluLmJlAHBhc3N3b3Jk
235 Authentication successful`
