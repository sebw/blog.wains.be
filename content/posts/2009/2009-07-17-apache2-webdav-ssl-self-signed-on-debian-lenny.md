---
date: 2009-07-17
title: "apache2 + webdav + SSL (self signed) on Debian Lenny"
---







categories:
- Apache
- Howto


**Install**

Install Apache2 and SSL

`apt-get install apache2 openssl ssl-cert`

Enable the Apache modules we'll be using :

`a2enmod ssl
a2enmod dav_fs
a2enmod dav`

Make sure you find the line "listen 443" somewhere in /etc/apache2/ports.conf


**Create the SSL certificate**

`mkdir /etc/apache2/ssl
openssl req $@ -new -x509 -days 365 -nodes -out /etc/apache2/ssl/apache.pem -keyout /etc/apache2/ssl/apache.pem
chmod 600 /etc/apache2/ssl/apache.pem`


**Apache config**

`mkdir -p /var/www/ssl/webdav/
chown www-data. /var/www/ssl/webdav/
htpasswd -c /var/www/passwd.dav user`


Edit /etc/apache2/sites-enabled/000-default like this :



    
    <code>
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/ssl/
    
            SSLEngine on
            SSLCertificateFile /etc/apache2/ssl/apache.pem
    
            
                    Options FollowSymLinks
                    AllowOverride None
            
            
                    Options Indexes FollowSymLinks MultiViews
                    AllowOverride None
                    Order allow,deny
                    allow from all
            
    
        # WEBDAV DIRECTORY
            
               DAV On
               AuthType Basic
               AuthName "webdav"
               AuthUserFile /var/www/passwd.dav 
               Require valid-user
           
    
            ErrorLog /var/log/apache2/error.log
            LogLevel warn
            CustomLog /var/log/apache2/access.log combined
    
    
    
            ServerAdmin webmaster@localhost
            DocumentRoot /var/www/non-ssl/
            
                    Options FollowSymLinks
                    AllowOverride None
            
            
                    Options Indexes FollowSymLinks MultiViews
                    AllowOverride None
                    Order allow,deny
                    allow from all
            
    
            ErrorLog /var/log/apache2/error.log
            LogLevel warn
            CustomLog /var/log/apache2/access.log combined
    </code>




**Restart Apache : **

`/etc/init.d/apache2 restart`


**Access the webdav share :**

The webdav share can be accessed by Windows, Linux or Mac machines out of the box.
Under Windows, you may need to have to change a key in the registry though.


**Sources : **
[http://www.howtoforge.com/setting-up-webdav-with-apache2-on-debian-etch](http://www.howtoforge.com/setting-up-webdav-with-apache2-on-debian-etch)
[http://longspine.com/node/10](http://longspine.com/node/10)

