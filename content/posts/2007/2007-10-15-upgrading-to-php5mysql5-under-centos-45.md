---
date: 2007-10-15
title: "Upgrading to PHP5/MySQL5 under CentOS 4.5"
---







categories:
- Linux
- Red Hat/CentOS
- SQL


I'm planning on moving to PHP 5 and MySQL 5 on our CentOS 4 production server.

Before doing anything on the production machine, I'm testing the process on a fresh installation of CentOS on a virtual machine...

I first tried to upgrade PHP... MySQL updates should be pulled out from the repository as well but it didn't work.


    
    <code># yum update php --enable=centosplus
    Setting up Update Process
    Setting up repositories
    update                    100% |=========================|  951 B    00:00     
    base                      100% |=========================| 1.1 kB    00:00     
    addons                    100% |=========================|  951 B    00:00     
    extras                    100% |=========================| 1.1 kB    00:00     
    Reading repository metadata in from local files
    primary.xml.gz            100% |=========================| 140 kB    00:00     
    update    : ################################################## 451/451
    Added 451 new packages, deleted 0 old in 19.89 seconds
    primary.xml.gz            100% |=========================| 580 kB    00:01     
    base      : ################################################## 1518/1518
    Added 1518 new packages, deleted 0 old in 55.58 seconds
    primary.xml.gz            100% |=========================|  157 B    00:00     
    Added 0 new packages, deleted 0 old in 0.04 seconds
    primary.xml.gz            100% |=========================|  54 kB    00:00     
    extras    : ################################################## 228/228
    Added 228 new packages, deleted 0 old in 6.44 seconds
    Resolving Dependencies
    --> Populating transaction set with selected packages. Please wait.
    ---> Package php.i386 0:5.1.6-3.el4s1.7 set to be updated
    --> Running transaction check
    --> Processing Dependency: php-cli = 5.1.6-3.el4s1.7 for package: php
    --> Processing Dependency: php = 4.3.9-3.22.9 for package: php-pear
    --> Processing Dependency: php = 4.3.9-3.22.9 for package: php-mysql
    --> Processing Dependency: php-common = 5.1.6-3.el4s1.7 for package: php
    --> Restarting Dependency Resolution with new changes.
    --> Populating transaction set with selected packages. Please wait.
    ---> Package php-cli.i386 0:5.1.6-3.el4s1.7 set to be updated
    ---> Package php-common.i386 0:5.1.6-3.el4s1.7 set to be updated
    ---> Package php-pear.noarch 1:1.4.11-1.el4s1.1 set to be updated
    ---> Package php-mysql.i386 0:5.1.6-3.el4s1.7 set to be updated
    --> Running transaction check
    --> Processing Dependency: php-pdo for package: php-mysql
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: php-mysql
    --> Processing Dependency: libmysqlclient.so.15 for package: php-mysql
    --> Restarting Dependency Resolution with new changes.
    --> Populating transaction set with selected packages. Please wait.
    ---> Package php-pdo.i386 0:5.1.6-3.el4s1.7 set to be updated
    ---> Package mysql.i386 0:5.0.27-1.el4.centos set to be updated
    --> Running transaction check
    --> Processing Dependency: libmysqlclient.so.14 for package: mysql-server
    --> Processing Dependency: libmysqlclient.so.14(libmysqlclient_14) for package: mysql-server
    --> Processing Dependency: libmysqlclient.so.14 for package: perl-DBD-MySQL
    --> Processing Dependency: libmysqlclient_r.so.14 for package: mysql-server
    --> Processing Dependency: libmysqlclient_r.so.14(libmysqlclient_14) for package: mysql-server
    --> Processing Dependency: mysql = 4.1.20-2.RHEL4.1.0.1 for package: mysql-server
    --> Restarting Dependency Resolution with new changes.
    --> Populating transaction set with selected packages. Please wait.
    ---> Package perl-DBD-MySQL.i386 0:3.0008-1.el4.centos set to be updated
    ---> Package mysql-server.i386 0:5.0.48-1.el4.centos set to be updated
    --> Running transaction check
    --> Processing Dependency: perl(:MODULE_COMPAT_5.8.8) for package: perl-DBD-MySQL
    --> Processing Dependency: mysql = 5.0.48-1.el4.centos for package: mysql-server
    --> Restarting Dependency Resolution with new changes.
    --> Populating transaction set with selected packages. Please wait.
    warning: package mysql = 5.0.27-1.el4.centos was already added, replacing with mysql  Package mysql.i386 0:5.0.48-1.el4.centos set to be updated
    ---> Package perl.i386 4:5.8.8-4.el4s1 set to be updated
    --> Running transaction check
    --> Processing Dependency: libmysqlclient_r.so.15 for package: mysql-server
    --> Processing Dependency: libmysqlclient.so.15 for package: php-mysql
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: php-mysql
    --> Processing Dependency: libmysqlclient.so.15 for package: mysql
    --> Processing Dependency: mysql-libs = 5.0.48-1.el4.centos for package: mysql
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: perl-DBD-MySQL
    --> Processing Dependency: libmysqlclient.so.15 for package: mysql-server
    --> Processing Dependency: libmysqlclient.so.15 for package: perl-DBD-MySQL
    --> Processing Dependency: libmysqlclient_r.so.15(libmysqlclient_15) for package: mysql-server
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: mysql-server
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: mysql
    --> Restarting Dependency Resolution with new changes.
    --> Populating transaction set with selected packages. Please wait.
    ---> Package mysql.i386 0:5.0.27-1.el4.centos set to be updated
    ---> Package mysql-libs.i386 0:5.0.48-1.el4.centos set to be updated
    --> Running transaction check
    
    Dependencies Resolved
    
    =============================================================================
     Package                 Arch       Version          Repository        Size 
    =============================================================================
    Updating:
     php                     i386       5.1.6-3.el4s1.7  centosplus        1.1 M
    Installing for dependencies:
     mysql-libs              i386       5.0.48-1.el4.centos  centosplus        1.8 M
     php-cli                 i386       5.1.6-3.el4s1.7  centosplus        2.0 M
     php-common              i386       5.1.6-3.el4s1.7  centosplus        135 k
     php-pdo                 i386       5.1.6-3.el4s1.7  centosplus        219 k
    Updating for dependencies:
     mysql                   i386       5.0.48-1.el4.centos  centosplus        2.7 M
     mysql                   i386       5.0.27-1.el4.centos  centosplus        3.3 M
     mysql-server            i386       5.0.48-1.el4.centos  centosplus        9.7 M
     perl                    i386       4:5.8.8-4.el4s1  centosplus         11 M
     perl-DBD-MySQL          i386       3.0008-1.el4.centos  centosplus        145 k
     php-mysql               i386       5.1.6-3.el4s1.7  centosplus         77 k
     php-pear                noarch     1:1.4.11-1.el4s1.1  centosplus        345 k
    
    Transaction Summary
    =============================================================================
    Install      4 Package(s)         
    Update       8 Package(s)         
    Remove       0 Package(s)         
    Total download size: 33 M
    Is this ok [y/N]: </code>



As you can notice, it pulls out 2 different versions of the package "mysql".

This is the explanation of the issue given on IRC :

` wolfy, toracat : well i'm working on a fresh install on a VM before I do anything on the production server.. So i assume I did nothing wrong.. it does try to pull out both version because it complains they are conflicting
 file /usr/bin/mysql conflicts between attempted installs of mysql-5.0.27-1.el4.centos and mysql-5.0.48-1.el4.centos
 sw:  what command are you running for the install?
 Evolution, yum update php --enablerepo=centosplus
 sw: that only pulls in php-mysql, which just needs mysql. you likely have mysql-server already installed
 and that's what's conflicting.
 Evolution: but shouldn't the new mysql obsolete the older one ?
 the installed version of mysql-server is mysql-server-4.1.20-2.RHEL4.1.0.1
 wolfy: it should and does, but php packaging changed, and that's caused a few issues in the upgrade.
 it's a valid mixed arch case, as php-pear went from an i386 arch to a noarch`

**The working command :**


    
    <code># yum --enablerepo centosplus install php php-pear php-mysql mysql mysql-server
    Setting up Install Process
    Setting up repositories
    update                    100% |=========================|  951 B    00:00     
    base                      100% |=========================| 1.1 kB    00:00     
    centosplus                100% |=========================|  951 B    00:00     
    addons                    100% |=========================|  951 B    00:00     
    extras                    100% |=========================| 1.1 kB    00:00     
    Reading repository metadata in from local files
    Parsing package install arguments
    Resolving Dependencies
    --> Populating transaction set with selected packages. Please wait.
    ---> Package php.i386 0:5.1.6-3.el4s1.7 set to be updated
    ---> Package mysql.i386 0:5.0.48-1.el4.centos set to be updated
    ---> Package mysql-server.i386 0:5.0.48-1.el4.centos set to be updated
    ---> Package php-pear.noarch 1:1.4.11-1.el4s1.1 set to be updated
    ---> Package php-mysql.i386 0:5.1.6-3.el4s1.7 set to be updated
    --> Running transaction check
    --> Processing Dependency: libmysqlclient_r.so.15 for package: mysql-server
    --> Processing Dependency: php-cli = 5.1.6-3.el4s1.7 for package: php
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: mysql
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: php-mysql
    --> Processing Dependency: libmysqlclient.so.15 for package: mysql
    --> Processing Dependency: php-pdo for package: php-mysql
    --> Processing Dependency: mysql-libs = 5.0.48-1.el4.centos for package: mysql
    --> Processing Dependency: php-common = 5.1.6-3.el4s1.7 for package: php-mysql
    --> Processing Dependency: libmysqlclient.so.15 for package: php-mysql
    --> Processing Dependency: libmysqlclient.so.15 for package: mysql-server
    --> Processing Dependency: php-common = 5.1.6-3.el4s1.7 for package: php
    --> Processing Dependency: libmysqlclient.so.15(libmysqlclient_15) for package: mysql-server
    --> Processing Dependency: libmysqlclient.so.14 for package: perl-DBD-MySQL
    --> Processing Dependency: libmysqlclient_r.so.15(libmysqlclient_15) for package: mysql-server
    --> Restarting Dependency Resolution with new changes.
    --> Populating transaction set with selected packages. Please wait.
    ---> Package php-pdo.i386 0:5.1.6-3.el4s1.7 set to be updated
    ---> Package mysql-libs.i386 0:5.0.48-1.el4.centos set to be updated
    ---> Package php-common.i386 0:5.1.6-3.el4s1.7 set to be updated
    ---> Package php-cli.i386 0:5.1.6-3.el4s1.7 set to be updated
    ---> Package perl-DBD-MySQL.i386 0:3.0008-1.el4.centos set to be updated
    --> Running transaction check
    --> Processing Dependency: perl(:MODULE_COMPAT_5.8.8) for package: perl-DBD-MySQL
    --> Restarting Dependency Resolution with new changes.
    --> Populating transaction set with selected packages. Please wait.
    ---> Package perl.i386 4:5.8.8-4.el4s1 set to be updated
    --> Running transaction check
    
    Dependencies Resolved
    
    =============================================================================
     Package                 Arch       Version          Repository        Size 
    =============================================================================
    Updating:
     mysql                   i386       5.0.48-1.el4.centos  centosplus        2.7 M
     mysql-server            i386       5.0.48-1.el4.centos  centosplus        9.7 M
     php                     i386       5.1.6-3.el4s1.7  centosplus        1.1 M
     php-mysql               i386       5.1.6-3.el4s1.7  centosplus         77 k
     php-pear                noarch     1:1.4.11-1.el4s1.1  centosplus        345 k
    Installing for dependencies:
     mysql-libs              i386       5.0.48-1.el4.centos  centosplus        1.8 M
     php-cli                 i386       5.1.6-3.el4s1.7  centosplus        2.0 M
     php-common              i386       5.1.6-3.el4s1.7  centosplus        135 k
     php-pdo                 i386       5.1.6-3.el4s1.7  centosplus        219 k
    Updating for dependencies:
     perl                    i386       4:5.8.8-4.el4s1  centosplus         11 M
     perl-DBD-MySQL          i386       3.0008-1.el4.centos  centosplus        145 k
    
    Transaction Summary
    =============================================================================
    Install      4 Package(s)         
    Update       7 Package(s)         
    Remove       0 Package(s)         
    Total download size: 30 M
    Is this ok [y/N]: y
    Downloading Packages:
    Running Transaction Test
    Finished Transaction Test
    Transaction Test Succeeded
    Running Transaction
      Updating  : perl                         ####################### [ 1/19] 
      Installing: php-common                   ####################### [ 2/19] 
      Installing: mysql-libs                   ####################### [ 3/19] 
      Updating  : perl-DBD-MySQL               ####################### [ 4/19] 
      Updating  : mysql                        ####################### [ 5/19] 
      Installing: php-pdo                      ####################### [ 6/19] 
      Installing: php-cli                      ####################### [ 7/19] 
      Updating  : php                          ####################### [ 8/19] 
      Updating  : php-pear                     ####################### [ 9/19] 
      Updating  : mysql-server                 ####################### [10/19] 
      Updating  : php-mysql                    ####################### [11/19] 
      Cleanup   : mysql-server                 ####################### [12/19]
      Cleanup   : php-mysql                    ####################### [13/19]
      Cleanup   : mysql                        ####################### [14/19]
      Cleanup   : php-pear                     ####################### [15/19]
      Cleanup   : perl                         ####################### [16/19]
      Removing  : perl-Filter                  ####################### [17/19]
      Cleanup   : php                          ####################### [18/19]
      Cleanup   : perl-DBD-MySQL               ####################### [19/19]
    
    Dependency Installed: mysql-libs.i386 0:5.0.48-1.el4.centos php-cli.i386 0:5.1.6-3.el4s1.7 php-common.i386 0:5.1.6-3.el4s1.7 php-pdo.i386 0:5.1.6-3.el4s1.7
    Updated: mysql.i386 0:5.0.48-1.el4.centos mysql-server.i386 0:5.0.48-1.el4.centos php.i386 0:5.1.6-3.el4s1.7 php-mysql.i386 0:5.1.6-3.el4s1.7 php-pear.noarch 1:1.4.11-1.el4s1.1
    Dependency Updated: perl.i386 4:5.8.8-4.el4s1 perl-DBD-MySQL.i386 0:3.0008-1.el4.centos
    Complete!</code>
