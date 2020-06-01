---
date: 2015-09-18
title: "Synology returns Illegal certificate when trying to import a valid certificate"
---
This happened to me yesterday as I was trying to import a valid Global-sign wildcard certificate.

You just need to edit your private key, the certificate and the intermediate certificate and remove all the comments outside the delimiter lines.

Example of how your certicate file should look like:

```
----BEGIN CERTIFICATE-----
<your certificate is here>
-----END CERTIFICATE-----
```

Example of a private key:

```
-----BEGIN RSA PRIVATE KEY-----
<your private key is here>
-----END RSA PRIVATE KEY-----
```

It should NOT look like this:

```
Some comments here
and here
----BEGIN CERTIFICATE-----
<your certificate is here>
-----END CERTIFICATE-----
And some more here
```
