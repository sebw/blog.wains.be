---
date: 2013-06-30
title: "Tomcat 6 webapp authentication against AD"
---







categories:
- Howto
- Security


_Tested on RHEL6_

**Add the following in /etc/tomcat6/server.xml (before the ending host tag) :**


    
    <realm userbase="ou=users,dc=intranet,dc=example,dc=org" referrals="follow" connectionpassword="password" usersubtree="true" connectionurl="ldap://intranet.example.org:389" rolebase="ou=users,dc=intranet,dc=example,dc=org" classname="org.apache.catalina.realm.JNDIRealm" rolesubtree="true" authentication="simple" usersearch="(sAMAccountName={0})" connectionname="username" debug="99" rolename="cn" rolesearch="(member={0})"></realm>



**Add your users to the group (role in Tomcat terms, which we'll call "myapplication" in this example) in AD.**

**Now edit /etc/tomcat6/tomcat-users.xml with the users :**


    
    
    <user name="user01" roles="myapplication"></user>
    




So here we have a group "myapplication" (matching query 'roleName=cn') with member=user01

**You webapp must be configured to require auth and define which roles are allowed, this is an example :**

WEB-INF/web.xml :


    
    
    <security-constraint>
        <web-resource-collection>
          <web-resource-name>Entire Application</web-resource-name>
          <url-pattern>/*</url-pattern>
        </web-resource-collection>
        <auth-constraint>
          <role-name>myapplication</role-name>
        </auth-constraint>
      </security-constraint>
    
      <login-config>
        <auth-method>BASIC</auth-method>
        <realm-name>Application</realm-name>
      </login-config>
    
      <security-role>
        <description>
          The role allowed in the app
        </description>
        <role-name>mysapplication</role-name>
      </security-role>
    



