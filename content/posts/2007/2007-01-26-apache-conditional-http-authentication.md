---
date: 2007-01-26
title: "Apache - conditional http authentication"
---







categories:
- Apache
- Howto
- Linux
- Security


This is what I needed to do :
I have a virtual host (say sub.domain.be) running under Apache web server at work that should be accessible for everybody on the local network but as well for a bunch of people outside of it. The main concern is *security*, we would consider the local network as safe while anything else is not.

Thus, the condition is this :
- local network : unrestricted access
- foreign network (in other words "web users") : http authentication



Here's the configuration I used on that virtual host :


    
    <code>AuthType        basic
    AuthName        "Sub Domain authentication"
    AuthUserFile    /var/www/virtual/.htmaster/.sub.domain.be
    Require         valid-user
    
    Satisfy         any
    Order           deny,allow
    Deny            from all
    Allow from      192.168.100.0/24</code>



What is interesting is the "Satisfy any" line.

[http://httpd.apache.org/docs/2.0/mod/core.html#satisfy](http://httpd.apache.org/docs/2.0/mod/core.html#satisfy) says this about it :
`Access policy if both Allow and Require used. The parameter can be either All or Any. This directive is only useful if access to a particular area is being restricted by both username/password and client host address. In this case the default behavior (All) is to require that the client passes the address access restriction and enters a valid username and password. **With the Any option the client will be granted access if they either pass the host restriction or enter a valid username and password**. This can be used to password restrict an area, but to let clients from particular addresses in without prompting for a password.`

So either the user is in the 192.168.100.0/24 range and gets unrestricted access to the virtual host, or he isn't and is asked for a username and password.

Keep in mind http authentication credentials are sent in the clear ! Force SSL encryption if you want the credentials to be encrypted.

Fore more info about "order directive", check this [link](http://blog.wains.be/post/apache-order-directive/)
