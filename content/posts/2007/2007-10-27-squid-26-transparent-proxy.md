---
date: 2007-10-27
title: "Squid 2.6 - transparent proxy"
---







categories:
- Howto
- Linux
- Proxy


I was explaining [in this article](http://blog.wains.be/post/transparent-squid/) how to enable the transparent proxy feature under Squid 2.5.

The following options required for transparent proxy are no longer available under Squid 2.6 :

`httpd_accel_host virtual
httpd_accel_port 80
httpd_accel_with_proxy on
httpd_accel_uses_host_header on `

The new option under Squid 2.6 is the much simpler :

`http_port 8080 transparent`

You just need to append "transparent" to the http_port option line.

If you upgrade to Squid 2.6 and don't update your config file you should get the following in the logs :

`messages:Oct 27 13:02:08 x squid: parseConfigFile: line 51 unrecognized: 'httpd_accel_host virtual' 
messages:Oct 27 13:02:08 x squid: parseConfigFile: line 52 unrecognized: 'httpd_accel_port 80' 
messages:Oct 27 13:02:08 x squid: parseConfigFile: line 53 unrecognized: 'httpd_accel_with_proxy on' 
messages:Oct 27 13:02:08 x squid: parseConfigFile: line 54 unrecognized: 'httpd_accel_uses_host_header on' `
