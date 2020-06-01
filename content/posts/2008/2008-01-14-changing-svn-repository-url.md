---
date: 2008-01-14
title: "Changing SVN repository URL"
---







categories:
- Linux
- Versioning


Not something I do on a daily basis, so here it goes :

`$ svn switch --relocate file:///home/old/path/to/svn/ file:///home/new/path/to/svn/`


You may also want to update the SVN repository URL in your TRAC environments...

`$ vim /path/to/trac-env/conf/trac.ini`

Update the variable named **repository_dir**

When done you need to resync the environments...

`$ trac-admin /path/to/trac-env/ resync`
