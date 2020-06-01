---
date: 2012-12-03
title: "Rundeck howto and examples"
---







categories:
- Automation
- Linux
- Tools


Quoting [rundeck.org](http://www.rundeck.org) : Rundeck is an Open Source process automation and command orchestration tool with a web console. As I understand it, it's a fork of Control Tier : [www.controltier.org](http://www.controltier.org) I'm usually all for the command-line, but you have to admit the devs have done a pretty good job regarding the web console. The [documentation](http://rundeck.org/docs/) is pretty good as well. No need to install agents on your servers. It works over SSH. You just need to deploy a dedicated public SSH key and you're done (see ssh-copy-id). This post should help you install and configure Rundeck in under 15 minutes. It covers configuration of email, SSL, authentication against Active Directory and explains how you can store your node definitions from a URL. **Installation on Red Hat :** basically a single RPM with no deps. You just need a working java. OpenJDK is working fine. **Email configuration (apparently not documented) : **Edit /etc/rundeck/rundeck-config.properties

    
    grails.mail.host=smtp.example.org
    grails.mail.port=25
    grails.mail.default.from=rundeck@example.org


**Enabling SSL on the web console (self-signed) :** See [http://rundeck.org/docs/administration/ssl.html ](http://rundeck.org/docs/administration/ssl.html )Basically :

    
    cd /etc/rundeck/ssl keytool -keystore keystore -alias rundeck -genkey -keyalg RSA -keypass password -storepass password
    cp /etc/rundeck/ssl/keystore /etc/rundeck/ssl/truststore


/etc/rundeck/framework.properties :

    
    framework.server.url = https://localhost:4443
    framework.rundeck.url = https://localhost:4443
    framework.server.port = 4443


Under /etc/rundeck/profile uncomment :

    
    export RDECK_JVM="$RDECK_JVM -Drundeck.ssl.config=/etc/rundeck/ssl/ssl.properties -Dserver.https.port=4443"


**Enabling LDAP against Active Directory for authentication :**/etc/rundeck/profile :

    
    export RDECK_JVM="-Djava.security.auth.login.config=/etc/rundeck/jaas-ldap.conf \
        -Dloginmodule.name=ldap \
        -Drdeck.config=/etc/rundeck \
        -Drdeck.base=/etc/rundeck \
        -Drundeck.server.configDir=/etc/rundeck \
        -Dserver.datastore.path=/var/lib/rundeck/data \
        -Drundeck.server.serverDir=/var/lib/rundeck \
        -Drdeck.projects=/var/rundeck/projects \
        -Drdeck.runlogs=/var/lib/rundeck/logs \
        -Drundeck.config.name=/etc/rundeck/rundeck-config.properties \
        -Djava.io.tmpdir=$RUNDECK_TEMPDIR"


/etc/rundeck/jaas-ldap.conf :

    
    ldap {
        com.dtolabs.rundeck.jetty.jaas.JettyCachingLdapLoginModule required
        debug="true"
        contextFactory="com.sun.jndi.ldap.LdapCtxFactory"
        providerUrl="ldap://intranet.example.org:389"
        bindDn="cn=queryldapaccount,ou=tech,ou=company,dc=intranet,dc=example,dc=org"
        bindPassword="xxx"
        authenticationMethod="simple"
        forceBindingLogin="true"
        userBaseDn="ou=company,dc=intranet,dc=example,dc=org"
        userRdnAttribute="sAMAccountName"
        userIdAttribute="sAMAccountName"
        userPasswordAttribute="unicodePwd"
        userObjectClass="user"
        roleBaseDn="OU=groups,OU=company,DC=intranet,DC=example,DC=org"
        roleNameAttribute="cn"
        roleMemberAttribute="member"
        roleObjectClass="group"
        cacheDurationMillis="300000"
        reportStatistics="true";
    };


**Configuring authorization :** You have the YAML file /etc/rundeck/admin.aclpolicy The following gives full access to Rundeck for members of **rundeck_superadmin **group, and limits execution of jobs under the group PRD/system for members of **rundeck_admin**. For "groups", see LDAP configuration, under roleBaseDn.

    
    description: Super Admin, all access.
    context:
      project: '.*' # all projects
    for:
      resource:
        - allow: '*' # allow read/create all kinds
      adhoc:
        - allow: '*' # allow running/killing adhoc jobs
      job:
        - allow: '*' # allow read/write/delete/run/kill of all jobs
      node:
        - allow: '*' # allow read/run for all nodes
    by:
      group: [rundeck_superadmin]
    
    ---
    
    description: Super Admin, all access.
    context:
      application: 'rundeck'
    for:
      resource:
        - allow: '*' # allow create of projects
      project:
        - allow: '*' # allow view/admin of all projects
    by:
      group: [rundeck_superadmin]
    
    <strong></strong>---
    
    description: Admin can run jobs under the PRD/system group.
    context:
      project: '.*' # all projects
    for:
      resource:
        - equals:
            kind: job
          allow: [read]
        - equals:
            kind: node
          allow: [read,create,update,refresh] 
        - equals:
            kind: event
          allow: [read,create] 
      adhoc:
        - allow: [read] 
      job:
        - equals:
            group: 'DEV'
          allow: [read]
        - equals:
            group: 'STAGING'
          allow: [read]
        - equals:
            group: 'PRD/cron'
          allow: [read]
        - equals:
            group: 'PRD/system'
          allow: [read, run, kill]
      node:
        - allow: [read,run] 
    by:
      group: [rundeck_admin]
    ---
    description: Admin Application level access control, applies to creating/deleting projects, admin of user profiles, viewing projects and reading system information.
    context:
      application: 'rundeck'
    for:
      resource:
        - equals:
            kind: project
          allow: [read]
        - equals:
            kind: system
          allow: [read] # allow read of system info
        - equals:
            kind: user
          allow: [read] # allow modify user profiles
      project:
        - match:
            name: '.*'
          allow: [read]
    by:
      group: [rundeck_admin]







You've deployed the SSH key and following those steps ? The web console is protected by HTTPS. You authenticate users against your Active Directory. You're almost good to go.







**Node inventory**







You can either edit a XML file under your project folder : /var/rundeck/projects/EXAMPLE/etc/resources.xml







This is what the file should look like :






    
    <?xml version="1.0" encoding="UTF-8"?>
    <!-- 20121203 17:41:12 -->
    <project>
    <node name="node1.intranet.example.org" type="Node"
    description="Node description"
    hostname="node1.intranet.example.org"
    username="root"
    osFamily="RHEL"
    osVersion="6"
    osArch="64"
    tags="EXAMPLE, OWNER, STAGING, WWW, ROOM_BXL, RACK10, PDU10_02"
    file-copy-destination-dir="/var/tmp/"
    />
    </project>







Regarding tags, imagination is your only limit. I personally specify the project manager name, room, role, if it's either on or off and the environment. You can filter using a mix of fields (e.g. : please display RHEL5 64 bits staging server in room X, your query would look something like : tags:ROOM_X+STAGING and osVersion:5 and osArch:64).







So you can either save the XML file locally, or you can call it from a URL. That's what I do by defining a URL Source under Resource Model Sources. I don't have a CMDB yet, so I manually update a CSV, and wrote a bash script generating the XML and making it available in a SVN repository (don't forget to set the MIME type application/xml to *.xml, see auto-props under your SVN configuration).







[![](https://blog.wains.be/images/Screen-Shot-2012-12-03-at-23.04.10-206x300.png)







Now, you're really good to go. You can start sending ad-hoc commands to your servers, or start looking into jobs. Rundeck jobs have replaced local crons on my servers. I don't store script on servers anymore. They are all stored in a SVN repository (they were already before Rundeck) and are called directly from Rundeck. I had to modify some of them,  look into "job options" as you don't want to store sensitive information in your scripts, as they are copied in the /tmp directory before being executed. You can see in my node definition that I specify this :






    
    file-copy-destination-dir="/var/tmp/"







By default, Rundeck will use /tmp but some of my servers have /tmp mounted as a partition with the noexec flag. This would produce an error in Rundeck.









