---
date: 2006-03-29
title: "courier-imap - secure pop3 and imap"
---

**Generate the key :**
$ openssl genrsa -out imap.key 1024

Result:

```
Generating RSA private key, 1024 bit long modulus
..............++++++
................................................................................................++++++
e is 65537 (0x10001)
```

**We need to generate a CSR :**
$ openssl req -new -key imap.key -out imap.csr

Result :
`You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [GB]:BE
State or Province Name (full name) [Berkshire]:Hainaut
Locality Name (eg, city) [Newbury]:Mons 
Organization Name (eg, company) [My Company Ltd]:Anything
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:hostname.domainname.tld
Email Address []:contact@domainname.tld
Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:`

**Generating a Self-Signed Certificate** :
At this point you will need to generate a self-signed certificate because you either don't plan on having your certificate signed by a CA, or you wish to test your new SSL implementation while the CA is signing your certificate. This temporary certificate will generate an error in the client email to the effect that the signing certificate authority is unknown and not trusted.

To generate a temporary certificate which is good for 10 years , issue the following command:

$ openssl x509 -req -days 3650 -in imap.csr -signkey imap.key -out imap.crt

Result : 
`Signature ok
subject=/C=BE/ST=Hainaut/L=Mons/O=Anything/CN=hostname.domainname.tld/emailAddress=contact@domainname.tld
Getting Private key
`

**Create the pem file for courier-imap :**
Remove any human readable version of the certificate which the signing process has inserted at the top of the new .crt file. 
Open it in an editor and remove everything down to the line which begins :
-----BEGIN CERTIFICATE-----

After you have removed that you need to put both the .key and .crt files into a .pem file for use with courier:
$ cat imap.key imap.crt > imap.pem

The last step in preparing this certificate for use with courier-imap is to add the Diffie-Hellman code to the .pem file:
$ openssl gendh >> imap.pem 

Result :
`Generating DH parameters, 512 bit long safe prime, generator 2
This is going to take a long time
...........+...........++*++*++*++*++*++* `
Actually it took less than 1 second :-)

Your courier-imap config should look like this (this is the imapd-ssl file):

`SSLPORT=993
SSLADDRESS=0
SSLPIDFILE=/var/run/imapd-ssl.pid
IMAPDSSLSTART=YES
IMAPDSTARTTLS=YES
IMAP_TLS_REQUIRED=0
COURIERTLS=/usr/lib/courier-imap/bin/couriertls
TLS_PROTOCOL=SSL3
TLS_STARTTLS_PROTOCOL=TLS1
TLS_CERTFILE=/usr/lib/courier-imap/share/certs/imap.pem`

Start the courier-imap service and try a connection using SSL, it should work
