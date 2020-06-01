---
date: 2006-11-22
title: "ModSecurity 2 - Invalid command SecRuleEngine, perhaps mis-spelled or"
---






  defined by a module not included in the server configuration'

categories:
- Apache
- Linux
- Security


If you try to install modsecurity2 on Apache without reading the docs, you may get this message :

**"Invalid command 'SecRuleEngine', perhaps mis-spelled or defined by a module not included in the server configuration"**

This is not a bug ! Just a RTFM alert ! :)

You should thoroughly follow the following procedure (as described [here ](http://www.modsecurity.org/documentation/modsecurity-apache/2.0.2/modsecurity2-apache-reference.html#02-installation)) to get modsecurity running :



ModSecurity installation consists of the following steps:

1. ModSecurity 2.x works with Apache 2.0.x or better.
2. Make sure you have mod_unique_id installed.
3. (Optional) Install the latest version of libxml2, if it isn't already installed on the server.
4. Unpack the ModSecurity archive
5. Edit Makefile to configure the path to Apache (for example: top_dir = /usr/local/apache2).
6. (Optional) Edit Makefile to enable ModSecurity to use libxml2 (uncomment line DEFS = -DWITH_LIBXML2) and configure the include path (for example: INCLUDES=-I/usr/include/libxml2)
7. Compile with make
8. Stop Apache
9. Install with make install
**10. (Optional) Add one line to your configuration to load libxml2: LoadFile /usr/lib/libxml2.so**
**11. Add one line to your configuration to load ModSecurity: LoadModule security2_module modules/mod_security2.so**
12. Configure ModSecurity
13. Start Apache
14. You now have ModSecurity 2.x up and running.

In bold, the two lines you may forget to add into your configuration when installing from RPM.
