---
date: 2007-03-03
title: "Hide PHP version in the header (X-Powered-By)"
---







categories:
- Apache
- Linux


**Edit /etc/php.ini (under RHEL/CentOS) and set :**
`expose_php = Off`



**Before :**
`$ curl -I blog.wains.be
HTTP/1.0 200 OK
Date: Sat, 03 Mar 2007 14:55:17 GMT
Server: Apache
X-Powered-By: PHP/4.x.x
X-Pingback: http://blog.wains.be/xmlrpc.php
Content-Type: text/html; charset=UTF-8
X-Cache: MISS from localhost
Connection: close`

**After :**
`$ curl -I blog.wains.be
HTTP/1.0 200 OK
Date: Sat, 03 Mar 2007 15:11:36 GMT
Server: Apache
X-Pingback: http://blog.wains.be/xmlrpc.php
Content-Type: text/html; charset=UTF-8
X-Cache: MISS from localhost
Connection: close`
