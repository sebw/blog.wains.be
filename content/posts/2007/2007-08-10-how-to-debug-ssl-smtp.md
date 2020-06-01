---
date: 2007-08-10
title: "How to debug SSL SMTP"
---







categories:
- Howto
- Security


My friend Jonathan just told me about a nice command to debug SSL SMTP :

`$ openssl s_client -connect mail.server.be:465`

Example : 

`$ openssl s_client -connect smtp.gmail.com:465
CONNECTED(00000003)
depth=0 /C=US/ST=California/L=Mountain View/O=Google Inc/CN=smtp.gmail.com
verify error:num=20:unable to get local issuer certificate
verify return:1
depth=0 /C=US/ST=California/L=Mountain View/O=Google Inc/CN=smtp.gmail.com
verify error:num=27:certificate not trusted
verify return:1
depth=0 /C=US/ST=California/L=Mountain View/O=Google Inc/CN=smtp.gmail.com
verify error:num=21:unable to verify the first certificate
verify return:1

Certificate chain
 0 s:/C=US/ST=California/L=Mountain View/O=Google Inc/CN=smtp.gmail.com
   i:/C=ZA/ST=Western Cape/L=Cape Town/O=Thawte Consulting cc/OU=Certification Services Division/CN=Thawte Premium Server CA/emailAddress=premium-server@thawte.com

Server certificate
-----BEGIN CERTIFICATE-----
MIIDYzCCAsygAwIBAgIQYZrZzKZNh1fKVFuUlZ6rKzANBgkqhkiG9w0BAQUFADCB
zjELMAkGA1UEBhMCWkExFTATBgNVBAgTDFdlc3Rlcm4gQ2FwZTESMBAGA1UEBxMJ
Q2FwZSBUb3duMR0wGwYDVQQKExRUaGF3dGUgQ29uc3VsdGluZyBjYzEoMCYGA1UE
CxMfQ2VydGlmaWNhdGlvbiBTZXJ2aWNlcyBEaXZpc2lvbjEhMB8GA1UEAxMYVGhh
d3RlIFByZW1pdW0gU2VydmVyIENBMSgwJgYJKoZIhvcNAQkBFhlwcmVtaXVtLXNl
cnZlckB0aGF3dGUuY29tMB4XDTA3MDczMDE2NTgwN1oXDTA4MDcyOTE2NTgwN1ow
aDELMAkGA1UEBhMCVVMxEzARBgNVBAgTCkNhbGlmb3JuaWExFjAUBgNVBAcTDU1v
dW50YWluIFZpZXcxEzARBgNVBAoTCkdvb2dsZSBJbmMxFzAVBgNVBAMTDnNtdHAu
Z21haWwuY29tMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDQF2mUMNM+qw/i
wMVSP2D0pgKb0M3RyWHBTQkno3W4y5TeH8LALnqv9/+Th4wZ5PrZ7YPQjmCxdtz6
Lm5Yx19nDXNw97or6SXvAoZSF+bwh76UFqxpImAGJzvj8Ro7rNkMidJa+KgGaIng
sIcWuqsj0rrK1AXoUHKmO4N5t0c6XwIDAQABo4GmMIGjMB0GA1UdJQQWMBQGCCsG
AQUFBwMBBggrBgEFBQcDAjBABgNVHR8EOTA3MDWgM6Axhi9odHRwOi8vY3JsLnRo
YXd0ZS5jb20vVGhhd3RlUHJlbWl1bVNlcnZlckNBLmNybDAyBggrBgEFBQcBAQQm
MCQwIgYIKwYBBQUHMAGGFmh0dHA6Ly9vY3NwLnRoYXd0ZS5jb20wDAYDVR0TAQH/
BAIwADANBgkqhkiG9w0BAQUFAAOBgQCUoTmFzdJX+2Pz9FhI+H88lFIeBcFnxpPO
CHO7zs/J3ZI6ZmkuQm4az89tRqvKvRFrQm2CRlzntqWjSdcsIYlKKGZ32iclpNKw
1aW/Q3IIyyZTTUo9DJezyCrFBV7JxFXOQgYd45+YxPVUNnkw1lTd4RqweuB5p7r4
nObS2EE7cA==
-----END CERTIFICATE-----
subject=/C=US/ST=California/L=Mountain View/O=Google Inc/CN=smtp.gmail.com
issuer=/C=ZA/ST=Western Cape/L=Cape Town/O=Thawte Consulting cc/OU=Certification Services Division/CN=Thawte Premium Server CA/emailAddress=premium-server@thawte.com

No client certificate CA names sent

SSL handshake has read 1025 bytes and written 308 bytes

New, TLSv1/SSLv3, Cipher is DES-CBC3-SHA
Server public key is 1024 bit
Compression: NONE
Expansion: NONE
SSL-Session:
    Protocol  : TLSv1
    Cipher    : DES-CBC3-SHA
    Session-ID: CC361F3727F3119BA597CDACDBA425B4E4BF91C788AA3F3812BD853E3D06B1BF
    Session-ID-ctx: 
    Master-Key: D99E897D9B128AFC1525E18BF0E2B55DA60D5357D3E8BEC497AC81561DF22A3C7F8B6EB58E01D7F9F5676BAB1B10FC0D
    Key-Arg   : None
    Start Time: 1186787801
    Timeout   : 300 (sec)
    Verify return code: 21 (unable to verify the first certificate)

220 mx.google.com ESMTP h7sm7864128nfh`

This will work with anything-SSL (pop3s, imaps, https, etc.)

Thanks Jonathan ! ;)
