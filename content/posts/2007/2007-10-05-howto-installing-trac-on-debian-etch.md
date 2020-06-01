---
date: 2007-10-05
title: "Howto - installing TRAC on Debian Etch"
---







categories:
- Apache
- Debian/Ubuntu
- Howto
- Versioning


Howto available here : [http://blog.wains.be/pub/howto/trac-debian.txt](http://blog.wains.be/pub/howto/trac-debian.txt)

This howto explains the steps from creating the SVN project to publishing it with TRAC.

Along with this howto, you can download a script that automates the process of creating the SVN repository and TRAC environment.
This script was made according to this howto and works under Debian. YOU SHOULD FIRST GO THROUGH THE HOWTO BEFORE USING THIS SCRIPT.

[http://blog.wains.be/pub/create_svntrac_project](http://blog.wains.be/pub/create_svntrac_project)

**Usage : **
 
`# create_svntrac_project.sh project_name (0|1)
project_name = NO space or special char
0 = SVN repository creation
1 = TRAC environment creation, SVN repository must exist and project must ALREADY BE imported`



I like TRAC because it provides a nice diff viewer (among other things).

I tried ViewCVS from the stable repo but I had several issues (broken images, no color in the diff viewer, etc.) 
I noticed in the code it was calling images from incorrect paths, etc.
Maybe I did something wrong, I don't know..

Anyway, TRAC is far better !
