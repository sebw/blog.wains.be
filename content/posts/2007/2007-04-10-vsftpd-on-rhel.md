---
date: 2007-04-10
title: "VSftpd on RHEL"
---







categories:
- Linux
- RHCE


**FTP server on RHEL**

RPM : vsftpd

Config : /etc/vsftpd/vsftpd.conf

By default :
- anonymous users will fall in a chroot located in /var/ftp/pub. They have read access only.
- local users are connecting in their /home and are not chrooted

In order to chroot local users use in the config :
`chroot_local_user=YES`

Thanks to Toutim for pointing out a mistake I've made in this article.
