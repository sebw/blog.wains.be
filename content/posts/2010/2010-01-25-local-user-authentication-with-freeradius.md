---
date: 2010-01-25
title: "Local user authentication with FreeRADIUS "
---







categories:
- Debian/Ubuntu
- Howto
- Networks
- RADIUS
- Wifi


This one is a bit less complex than [http://blog.wains.be/post/wpa2-freeradius-eap-tls/](http://blog.wains.be/post/wpa2-freeradius-eap-tls/)

This is actually the most basic RADIUS configuration ever, useful for quick tests. I can only recommend checking the post mentioned above if you want to do something serious. 

`# apt-get install freeradius`


    
    <code># vim /etc/freeradius/users
    
    login     Cleartext-Password := "password"
    login2     Cleartext-Password := "password2"</code>





    
    <code>#vim /etc/freeradius/clients.conf
    
    client localhost {
    	ipaddr = 127.0.0.1
            secret = radiuspassword
    }
    
    client router {
    	ipaddr = 10.0.0.1
            secret = radiuspassword
    }</code>




`# /etc/init.d/freeradius restart`


Check if RADIUS is working :

`# radtest login password localhost 1812 radiuspassword 
Sending Access-Request of id 222 to 127.0.0.1 port 1812
	User-Name = "login"
	User-Password = "password"
	NAS-IP-Address = 127.0.1.1
	NAS-Port = 1812
rad_recv: Access-Accept packet from host 127.0.0.1 port 1812, id=222, length=20`

`# radtest login2 password2 localhost 1812 radiuspassword 
Sending Access-Request of id 1 to 127.0.0.1 port 1812
	User-Name = "login2"
	User-Password = "password2"
	NAS-IP-Address = 127.0.1.1
	NAS-Port = 1812
rad_recv: Access-Accept packet from host 127.0.0.1 port 1812, id=1, length=20`


We expect Access-Accept from the server, not Access-Reject...


**Configure your wireless access point :**

Usually found under Security tab (or RADIUS, 802.1X, etc.)
Configure your device at 10.0.0.1 to authenticate against the Radius server with password radiuspassword.
Try to connect to your wireless access point using login and password.


