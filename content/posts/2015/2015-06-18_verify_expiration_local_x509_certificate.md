---
date: 2015-06-18
title: "Verify expiration date for a local x509 certificate"
---
Date: 2015-06-18

Use this command:

```
openssl x509 -noout -in /etc/pki/tls/certs/client.crt -dates
```

Output would be:

```
notBefore=Feb 20 16:20:08 2015 GMT
notAfter=Feb 20 16:20:08 2016 GMT
```
