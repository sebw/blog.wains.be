---
date: 2008-06-05
title: "Debian - how to keep a mixed system"
---







categories:
- Debian/Ubuntu
- Linux


**Create or edit /etc/apt/apt.conf with this content :**

`APT::Default-Release "stable";`

**Edit your APT source list /etc/apt/sources.list as the following (adapt to your favorite mirrors) :**


    
    <code># unstable
    deb http://ftp.debian.skynet.be/ftp/debian/ unstable main
    deb-src http://ftp.debian.skynet.be/ftp/debian/ unstable main
    
    # stable
    deb http://ftp.debian.skynet.be/ftp/debian/ etch main
    deb-src http://ftp.debian.skynet.be/ftp/debian/ etch main
    
    # security updates
    deb http://security.debian.org/ etch/updates main contrib
    deb-src http://security.debian.org/ etch/updates main contrib</code>



**Now whenever you want to install a package in "unstable" on your "stable" system do the following :**

`aptitude -t unstable install packagename`
