---
date: 2007-07-11
title: "Howto install Munin on CentOS"
---







categories:
- Howto
- Linux
- Security


From : http://blog.jploh.com/post/how-to-install-munin-on-centos/

Munin is a monitoring tool for servers. It uses RRDtool to log and graph data from your servers. The plugin API is very easy to grasp. Actually, I haven’t read the API documentation yet. I just looked at the output of the plugins and it looks easy to achieve. The data can be accessed through the web.

This guide will walk you through installing and configuring Munin on CentOS 4.x. 
The steps are pretty much the same for later releases of CEntOS, Red Hat, Fedora [Core] and/or Red Hat-based installations.



Munin works by polling your servers for the data hence two applications, Munin and Munin Node. The former periodically gathers data (cronned) and the latter serves the data to the former. Please refer to the following for our example configuration. You can make up a domain if you want Munin to group your servers similar to the live demo.

Munin "Graph Server" - alpha.sample.net (192.168.1.1)
A Munin Node "client" - bravo.sample.net (192.168.1.2)

**1. Installing and Configuring Munin (server-side)**

In this section, we set it up on alpha.sample.net

   1. Add the RPMforge repository.
      rpm -Uhv http://apt.sw.be/packages/rpmforge-release/rpmforge-release-0.3.6-1.el4.rf.i386.rpm
      This step is optional if your Linux distribution has the packages in its default repositories. 
   2. Install munin.
      yum -y install munin
   3. Change the ownership of the Munin web docroot to munin.
      chown -R munin:munin /var/www/munin
   4. The default configuration file (in version 1.2.5-1) the value for the web docroot points to the wrong directory. Replace the value of htmldir from /var/www/html/munin to /var/www/munin at /etc/munin/munin.conf (line 7).
   5. Restart Apache and Cron
      service httpd restart && service crond restart
   6. You can check if it’s working through your browser (i.e. http://192.168.1.1/munin/).

**2. Add a Node (clients)**

In this section, we will configure bravo.sample.net.

   1. Add the RPMforge repository (see 1.1).
   2. Install Munin Node.
      yum -y install munin-node
   3. Configure. Edit /etc/munin/munin-node.conf with your favorite text editor.
          * Allow the graph server (alpha.sample.net/192.168.1.1) to poll the node.
            allow ^192.168.1.1$
          * If your server doesn’t report the correct hostname, add the following line
            host_name bravo.sample.net
          * If your servers have two interfaces and on the same LAN (e.g. one for Internet and another for LAN), you can configure the node to bind and listen on the local interface by changing the value of host (line 13) from * to the local IP of the node.
   4. Start munin-node and set to start on bootup.
      service munin-node start
      chkconfig munin-node on
   5. Edit Munin’s configuration on the graph server (/etc/munin/munin.conf).
      [bravo.sample.net]
      address 192.168.1.2
      use_node_name yes
6. Open port tcp/4949 in the firewall of the node

Wait for at least 5 minutes for the new node to appear. You can also install the node on the graph server. The default node configuration will work out of the box.

**3. Install/Activate Some Plugins**

This section should familiarize you with the plugin installation routine. Plugins are installed in the nodes.

    * Apache
         1. Create a symbolic link to the Apache plugins (stored in /usr/share/munin/plugins) in the plugin folder.
            ln -s /usr/share/munin/plugins/apache_* /etc/munin/plugins/
         2. Enable server status reports. Add the following to Apache’s configuration file.
            ExtendedStatus On
            
            SetHandler server-status
            Order Deny,Allow
            Deny from all
            Allow from 127.0.0.1
            
         3. Restart Apache and the node
            service httpd restart && service munin-node restart
    * Asterisk
         1. Download the plugins for your Asterisk version from here.
         2. Extract them to /usr/share/munin/plugins.
         3. Make the files executable
            chmod 755 /usr/share/munin/plugins/asterisk_*
         4. Configure Asterisk Manager by adding/changing the following in /etc/asterisk/manager.conf
            [general]
            enabled = yes
            port = 5038

            [munin]
            secret = somepassword
            permit = 127.0.0.1 ;if this doesn’t work, use the local IP
            write = system,call,log,verbose,command,agent,user
         5. Add the following to the plugin configuration file in /etc/munin/plugin-conf.d/munin-node
            [asterisk_*]
            env.username munin
            env.secret somepassword
         6. Reload Asterisk’s configuration and restart the node.
            asterisk -rx reload >> /dev/null && service munin-node restart
    * MySQL
         1. Create a symbolic link to the MySQL plugins (stored in /usr/share/munin/plugins) in the plugin folder.
            ln -s /usr/share/munin/plugins/mysql_* /etc/munin/plugins/
         2. If your root user has a password (or want to use a different user), edit the plugin configuration file in /etc/munin/plugin-conf.d/munin-node and uncomment line 16 by removing the leading hash (#). Then change the parameters that will be used when mysqladmin is run.
         3. Restart the node
            service munin-node restart
    * MTR
         1. Make sure you have the latest version of MTR.
            yum -y install mtr && yum -y update mtr
         2. Download the plugin here (direct link)
         3. Extract to /usr/share/munin/plugins
         4. Make the file executable.
            chmod 755 /usr/share/munin/plugins/mtr100_
         5. Create a symbolic link to the plugin (stored in /usr/share/munin/plugins) in the plugin folder. Append the host that you want to query to the link of the name.
            ln -s /usr/share/munin/plugins/mtr100_ /etc/munin/plugins/mtr100_somehost.com
         6. To add another host to query, just create another symbolic link.
         7. Add the following to the plugin configuration file in /etc/munin/plugin-conf.d/munin-node
            [mtr100_*]
            timeout 60
         8. Restart the node
            service munin-node restart

