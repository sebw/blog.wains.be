---
date: 2007-04-23
title: "Installing ZoneMinder 1.22.3 under CentOS 4.4"
---







categories:
- Linux


http://www.zoneminder.com/

ZoneMinder is intended for use in single or multi-camera video security applications, including commercial or home CCTV, theft prevention and child or family member or/ home monitoring and other care scenarios....



01. Disable SELinux (that's the easy way, I know.. please provide the steps in the comment if you kept SELinux on) :
`Edit /etc/sysconfig/selinux`

02. Install RPMforge repository :
`See http://dag.wieers.com/rpm/FAQ.php#B`

03. Install necessary packages : 
`# yum install mysql-server mysql php-mysql mysql-devel libjpeg-devel pcre-devel subversion ffmpeg perl-Archive-Tar perl-MIME-Lite perl-MIME-tools gcc gcc-c++`

In the comments you can see some people had to install the following packages as well :
perl-Time-HiRes

I'm no longer using CentOS 4 so I can't verify. Thanks for the feedback guys !

04. Start mysql and Apache :
`# service mysqld start

05. Download and unzip Zoneminder : 
`$ wget http://www.zoneminder.com/downloads/ZoneMinder-1.22.3.tar.gz
$ gzip -d ZoneMinder-1.22.3.tar.gz && tar -xvf ZoneMinder-1.22.3.tar`

06. Compile and install :
`$ ./configure --with-webdir=/var/www/html/zm --with-cgidir=/var/www/cgi-bin
$ make && make install`

07. Create zm database :
`$ mysql -p
> CREATE DATABASE `zm` DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;`

08. Import data : 
`$ mysql -p zm < /source/dir/db/zm_create.sql`

09. Create user zmuser and the rights :
`$ mysql -p 
> GRANT USAGE ON * . * TO 'zmuser'@'localhost' IDENTIFIED BY 'zmpass' WITH MAX_QUERIES_PER_HOUR 0 MAX_CONNECTIONS_PER_HOUR 0 MAX_UPDATES_PER_HOUR 0 ;
> GRANT SELECT , INSERT , UPDATE , DELETE ON `zm` . * TO 'zmuser'@'localhost';`

10. Make Zoneminder start at boot :
`# cp /source/dir/scripts/zm /etc/init.d ; chkconfig --add /etc/init.d/zm`
