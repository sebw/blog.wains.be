---
date: 2006-03-05
title: "dsniff working under CentOS 4.2"
---

I've been able to get dsniff working under CentOS 4.2..

I just needed to find the right packages, right versions, etc

You'll need to downgrade some packages version and it may break your system !
- Ethereal will stop working after applying these packages.
- You'll need to downgrade to compat-db-4.0.14-2, gnome-libs depends on compat-db-4.1.25-9 (gnome is still running fine but that's risky)

You can always compile these packages by hand :
[http://www.enzotech.net/files/dsniff-2.4.fixed.FC.tar.gz](http://www.enzotech.net/files/dsniff-2.4.fixed.FC.tar.gzhttp://www.enzotech.net/files/dsniff-2.4.fixed.FC.tar.gz)
[http://www.enzotech.net/files/libnids-1.16-fixed-FC.tar.gz](http://www.enzotech.net/files/libnids-1.16-fixed-FC.tar.gz)
[http://www.enzotech.net/files/libnet-1.0.2a-fixed-FC.tar.gz
](http://www.enzotech.net/files/libnet-1.0.2a-fixed-FC.tar.gz)
Still, it will always need libpcap.so.0.6.3 but you'll be running the latest version of dsniff
