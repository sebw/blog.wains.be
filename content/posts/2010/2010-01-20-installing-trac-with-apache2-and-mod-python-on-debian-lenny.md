---
date: 2010-01-20
title: "Installing TRAC with Apache2 and mod-python on Debian Lenny"
---







categories:
- Apache
- Debian/Ubuntu
- Howto
- Versioning


This is based on a fresh install.

Install the necessary stuff :

`# apt-get install apache2 subversion trac

libapache2-svn will enable dav and dav_fs modules.

More stuff :

`# apt-get install libapache2-mod-python`

Create your directories for TRAC environments (/home/trac/), projects source files (/home/dev/) and SVN repositories (/home/svn/) :

`# mkdir /home/{trac,dev,svn}`

Create your first project :

`# mkdir /home/dev/project1

Create the SVN repository for the project :

`# svnadmin create /home/svn/project1`

Import the project into the SVN repository :


    
    <code># svn import -m "Initial import" /home/dev/project1/ file:///home/svn/project1/
    Adding         /home/dev/project1/index.php
    
    Committed revision 1.</code>



Move your sources to a safe place, while we checkout the project :

`# mv /home/dev/project1 /home/dev/project1-orig`

Checkout the project :

`# svn checkout file:///home/svn/project1 /home/dev/project1
A    /home/dev/project1/index.php
Checked out revision 1.`

Make sure the project is now under revision, you should see a ".svn" directory :

`# ls -lah /home/dev/project1
total 16K
drwxr-xr-x 3 root root 4.0K Jan 20 12:42 .
drwxr-xr-x 4 root root 4.0K Jan 20 12:42 ..
drwxr-xr-x 6 root root 4.0K Jan 20 12:42 .svn
-rw-r--r-- 1 root root   19 Jan 20 12:42 index.php`

It's now safe to delete the copy not under revision :

`# rm -fr /home/dev/project1-orig/`

Set up TRAC for your first project, in bold what you need to specify :


    
    <code># trac-admin /home/trac/project1 initenv
    Creating a new Trac environment at /home/trac/project1
    
    Trac will first ask a few questions about your environment 
    in order to initialize and prepare the project database.
    
     Please enter the name of your project.
     This name will be used in page titles and descriptions.
    
    Project Name [My Project]> <strong>Project1</strong>
     
     Please specify the connection string for the database to use.
     By default, a local SQLite database is created in the environment
     directory. It is also possible to use an already existing
     PostgreSQL database (check the Trac documentation for the exact
     connection string syntax).
    
    Database connection string [sqlite:db/trac.db]> <strong>PRESS ENTER</strong>
     
     Please specify the type of version control system,
     By default, it will be svn.
    
     If you don't want to use Trac with version control integration,
     choose the default here and don't specify a repository directory.
     in the next question.
    
    Repository type [svn]> <strong>svn</strong>
    
     Please specify the absolute path to the version control
     repository, or leave it blank to use Trac without a repository.
     You can also set the repository location later.
    
    Path to repository [/path/to/repos]> <strong>/home/svn/project1</strong>
    
    Creating and Initializing Project
     Installing default wiki pages
     TracSyntaxColoring imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracSyntaxColoring
     TracChangeset imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracChangeset
     TracWiki imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracWiki
     WikiHtml imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiHtml
     TracRevisionLog imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracRevisionLog
     TracFastCgi imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracFastCgi
     TracTicketsCustomFields imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracTicketsCustomFields
     SandBox imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/SandBox
     WikiMacros imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiMacros
     TracUpgrade imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracUpgrade
     TracBackup imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracBackup
     TracAccessibility imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracAccessibility
     RecentChanges imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/RecentChanges
     WikiDeletePage imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiDeletePage
     TracNavigation imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracNavigation
     TracImport imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracImport
     TracModPython imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracModPython
     TracEnvironment imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracEnvironment
     TracBrowser imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracBrowser
     WikiFormatting imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiFormatting
     TracPlugins imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracPlugins
     WikiPageNames imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiPageNames
     TracNotification imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracNotification
     TracInstall imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracInstall
     TracIni imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracIni
     TracAdmin imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracAdmin
     TracRss imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracRss
     TracLogging imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracLogging
     TracGuide imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracGuide
     WikiStart imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiStart
     TracQuery imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracQuery
     WikiNewPage imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiNewPage
     CamelCase imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/CamelCase
     TracRoadmap imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracRoadmap
     TracLinks imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracLinks
     TracStandalone imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracStandalone
     TracInterfaceCustomization imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracInterfaceCustomization
     TracUnicode imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracUnicode
     InterMapTxt imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/InterMapTxt
     TracPermissions imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracPermissions
     TitleIndex imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TitleIndex
     WikiProcessors imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiProcessors
     InterWiki imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/InterWiki
     TracCgi imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracCgi
     TracTimeline imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracTimeline
     InterTrac imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/InterTrac
     PageTemplates imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/PageTemplates
     TracTickets imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracTickets
     TracSupport imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracSupport
     TracWorkflow imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracWorkflow
     TracSearch imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracSearch
     TracFineGrainedPermissions imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracFineGrainedPermissions
     WikiRestructuredTextLinks imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiRestructuredTextLinks
     TracReports imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/TracReports
     WikiRestructuredText imported from /usr/lib/python2.5/site-packages/trac/wiki/default-pages/WikiRestructuredText
     Indexing repository
     [1]
    ---------------------------------------------------------------------
    Project environment for 'Project1' created.
    
    You may now configure the environment by editing the file:
    
      /home/trac/project1/conf/trac.ini
    
    If you'd like to take this new project environment for a test drive,
    try running the Trac standalone web server `tracd`:
    
      tracd --port 8000 /home/trac/project1
    
    Then point your browser to http://localhost:8000/project1.
    There you can also browse the documentation for your installed
    version of Trac, including information on further setup (such as
    deploying Trac to a real web server).
    
    The latest documentation can also always be found on the project
    website:
    
      http://trac.edgewall.org/
    
    Congratulations</code>



The configuration is stored under /home/trac/project1/conf/trac.ini.

Create the password files for web authentication :

`# htpasswd -c /etc/apache2/passwd-trac yourusername`

Set up Apache :

`# cp /etc/apache2/sites-available/default /etc/apache2/sites-available/projects`


    
    <code># vim /etc/apache2/sites-available/projects
    
        DocumentRoot /var/www/
    
        
            Order allow,deny
            Allow from all
        
    
    
        ### TRAC Root : http://server/trac or http://server/trac/
    
            # Rewrite ./trac to ./trac/
            RewriteEngine on
            RewriteRule ^(.*)/trac$ $1/ [NC]
    
        
            SetHandler mod_python
            PythonHandler trac.web.modpython_frontend
            PythonInterpreter main
            PythonOption TracEnvParentDir /home/trac
            PythonOption TracUriRoot /trac/
            SetEnv PYTHON_EGG_CACHE /tmp
        
    
        ### TRAC Login : http://server/trac/*/login
        
            AuthType Basic
            AuthName "TRAC Login"
            AuthUserFile /etc/apache2/passwd-trac
            Require valid-user
        
    
        ### SVN repository : http://server/svn
        
            DAV svn
            SVNParentPath /home/svn
            SVNListParentPath on
    
            AuthType Basic
            AuthName "SVN Repository"
            AuthUserFile /etc/apache2/passwd-trac
            Require valid-user
        
    </code>



Enable rewrite module :

`# a2enmod rewrite
Enabling module rewrite.
Run '/etc/init.d/apache2 restart' to activate new configuration!`

Disable the default website :

`# a2dissite default
Site default disabled.
Run '/etc/init.d/apache2 reload' to activate new configuration!`

Enable the newly configured website :

`# a2ensite projects
Enabling site projects.
Run '/etc/init.d/apache2 reload' to activate new configuration!`

Restart Apache :

`# /etc/init.d/apache2 restart`

Make sure Apache can read and write TRAC configuration files.
This is a basic working example but you may want to do something more elaborate involving Set-GID or POSIX ACL.

`# chown -R www-data. /home/trac`



Now go to http://server/trac, it should rewrite the URL to http://server/trac/ and display a list of available projects.


**WALLA ;)**


Please let me know if it works for you. Thanks.


Partially based on [http://www.willamaze.eu/?p=732](http://www.willamaze.eu/?p=732)
