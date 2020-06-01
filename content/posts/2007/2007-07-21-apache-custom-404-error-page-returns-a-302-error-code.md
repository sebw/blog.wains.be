---
date: 2007-07-21
title: "Apache - custom 404 error page returns a 302 error code"
---







categories:
- Apache
- Linux


First let's look what the docs says about setting up a custom error page :
[http://httpd.apache.org/docs/2.0/en/custom-error.html](http://httpd.apache.org/docs/2.0/en/custom-error.html)

**If you set up your custom 404 error page pointing to a full URL like :**

`ErrorDocument 404 http://blog.wains.be/404/index.html`

You would get an error 302 code.



`$ curl -I http://blog.wains.be/mlfkgldk
HTTP/1.1 302 Found
Date: Sat, 21 Jul 2007 09:20:40 GMT
Server: Apache
Location: http://blog.wains.be/404/index.html
Connection: close
Content-Type: text/html; charset=iso-8859-1`

**If you want to get the real deal :**

`ErrorDocument 404 /404/index.html`

Then...

`$ curl -I http://blog.wains.be/mlfkgldk
HTTP/1.1 404 Not Found
Date: Sat, 21 Jul 2007 09:21:16 GMT
Server: Apache
Last-Modified: Sat, 21 Jul 2007 09:19:53 GMT
ETag: "594022-307-8bd8a040"
Accept-Ranges: bytes
Content-Length: 775
Connection: close
Content-Type: text/html; charset=ISO-8859-1`

**Useful link :**

[HTTP/1.1 status code definitions available here](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)


