---
date: 2010-01-15
title: "Asterisk dependencies on Debian Lenny or Squeeze ??"
---







categories:
- Asterisk
- Debian/Ubuntu


Can someone explain why build-essential is a dependency of Asterisk under Lenny or Squeeze ? 

142 MB.. seriously ? Meanwhile [Askozia](http://www.askozia.com) fits on 30 MB, and that includes the OS.


    
    <code># apt-get install asterisk
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following extra packages will be installed:
      asterisk-config asterisk-sounds-main binutils build-essential bzip2 ca-certificates cpp cpp-4.3 debhelper dpkg-dev file g++ g++-4.3 gcc gcc-4.3 gettext gettext-base
      html2text intltool-debian libasound2 libc-client2007b libc6-dev libcap2 libcompress-raw-zlib-perl libcompress-zlib-perl libcurl3 libdigest-hmac-perl libdigest-sha1-perl
      libfile-remove-perl libgmp3c2 libgomp1 libgsm1 libidn11 libiksemel3 libio-compress-base-perl libio-compress-zlib-perl libio-stringy-perl libldap-2.4-2 libltdl3
      libmagic1 libmail-box-perl libmail-sendmail-perl libmailtools-perl libmime-types-perl libmpfr1ldbl libobject-realize-later-perl libogg0 libpci3 libperl5.10 libpq5
      libpri1.0 libradiusclient-ng2 libsensors3 libsnmp-base libsnmp15 libspeex1 libspeexdsp1 libsqlite0 libssh2-1 libstdc++6-4.3-dev libsys-hostname-long-perl libsysfs2
      libtimedate-perl libtonezone1 liburi-perl libuser-identity-perl libvorbis0a libvorbisenc2 libvpb0 linux-libc-dev make mlock module-assistant odbcinst1debian1 openssl
      patch perl perl-modules po-debconf ucf unixodbc vpb-driver-source
    Suggested packages:
      ekiga ohphone twinkle kphone asterisk-doc asterisk-dev asterisk-h323 binutils-doc bzip2-doc cpp-doc gcc-4.3-locales dh-make debian-keyring g++-multilib g++-4.3-multilib
      gcc-4.3-doc libstdc++6-4.3-dbg gcc-multilib manpages-dev autoconf automake1.9 libtool flex bison gdb gcc-doc gcc-4.3-multilib libmudflap0-4.3-dev libgcc1-dbg
      libgomp1-dbg libmudflap0-dbg cvs gettext-doc libasound2-plugins uw-mailutils glibc-doc libmime-tools-perl libhtml-tree-perl libhtml-format-perl spamassassin
      libmail-imapclient-perl lm-sensors speex libstdc++6-4.3-doc libwww-perl vpb-utils make-doc diff-doc perl-doc libterm-readline-gnu-perl libterm-readline-perl-perl
      libmyodbc odbc-postgresql libct1
    The following NEW packages will be installed:
      asterisk asterisk-config asterisk-sounds-main binutils build-essential bzip2 ca-certificates cpp cpp-4.3 debhelper dpkg-dev file g++ g++-4.3 gcc gcc-4.3 gettext
      gettext-base html2text intltool-debian libasound2 libc-client2007b libc6-dev libcap2 libcompress-raw-zlib-perl libcompress-zlib-perl libcurl3 libdigest-hmac-perl
      libdigest-sha1-perl libfile-remove-perl libgmp3c2 libgomp1 libgsm1 libidn11 libiksemel3 libio-compress-base-perl libio-compress-zlib-perl libio-stringy-perl
      libldap-2.4-2 libltdl3 libmagic1 libmail-box-perl libmail-sendmail-perl libmailtools-perl libmime-types-perl libmpfr1ldbl libobject-realize-later-perl libogg0 libpci3
      libperl5.10 libpq5 libpri1.0 libradiusclient-ng2 libsensors3 libsnmp-base libsnmp15 libspeex1 libspeexdsp1 libsqlite0 libssh2-1 libstdc++6-4.3-dev
      libsys-hostname-long-perl libsysfs2 libtimedate-perl libtonezone1 liburi-perl libuser-identity-perl libvorbis0a libvorbisenc2 libvpb0 linux-libc-dev make mlock
      module-assistant odbcinst1debian1 openssl patch perl perl-modules po-debconf ucf unixodbc vpb-driver-source
    0 upgraded, 83 newly installed, 0 to remove and 0 not upgraded.
    Need to get 47.3MB of archives.
    After this operation, 142MB of additional disk space will be used.
    Do you want to continue [Y/n]? </code>



**EDIT Janv. 30 :**

Thanks to Kurt for the tip in the comments.
It completely went unnoticed to me, but Debian Lenny indeed installs "recommends" packages :

**To avoid the bloat caused by this new policy, edit /etc/apt/apt.conf and add :**
`APT::Install-Recommends "0";`

The result is clear : 


    
    <code># apt-get install asterisk
    Reading package lists... Done
    Building dependency tree       
    Reading state information... Done
    The following extra packages will be installed:
      asterisk-config asterisk-sounds-main ca-certificates libasound2
      libc-client2007b libcap2 libcurl3 libgsm1 libidn11 libiksemel3 libldap-2.4-2
      libltdl3 libogg0 libpci3 libperl5.10 libpq5 libpri1.0 libradiusclient-ng2
      libsensors3 libsnmp-base libsnmp15 libspeex1 libspeexdsp1 libsqlite0
      libssh2-1 libsysfs2 libtonezone1 libvorbis0a libvorbisenc2 libvpb0 mlock
      odbcinst1debian1 openssl ucf unixodbc
    Suggested packages:
      ekiga ohphone twinkle kphone asterisk-doc asterisk-dev asterisk-h323
      libasound2-plugins uw-mailutils lm-sensors speex vpb-utils libmyodbc
      odbc-postgresql libct1
    Recommended packages:
      vpb-driver-source
    The following NEW packages will be installed:
      asterisk asterisk-config asterisk-sounds-main ca-certificates libasound2
      libc-client2007b libcap2 libcurl3 libgsm1 libidn11 libiksemel3 libldap-2.4-2
      libltdl3 libogg0 libpci3 libperl5.10 libpq5 libpri1.0 libradiusclient-ng2
      libsensors3 libsnmp-base libsnmp15 libspeex1 libspeexdsp1 libsqlite0
      libssh2-1 libsysfs2 libtonezone1 libvorbis0a libvorbisenc2 libvpb0 mlock
      odbcinst1debian1 openssl ucf unixodbc
    0 upgraded, 36 newly installed, 0 to remove and 13 not upgraded.
    Need to get 14.0MB of archives.
    After this operation, 33.2MB of additional disk space will be used.
    Do you want to continue [Y/n]?</code>



