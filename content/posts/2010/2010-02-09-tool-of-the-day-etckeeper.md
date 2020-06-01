---
date: 2010-02-09
title: "Tool of the day - etckeeper"
---







categories:
- Linux
- Versioning


[http://joey.kitenet.net/code/etckeeper/](http://joey.kitenet.net/code/etckeeper/)

`etckeeper is a collection of tools to let /etc be stored in a git, mercurial, darcs, or bzr repository. It hooks into apt (and other package managers including yum and pacman-g2) to automatically commit changes made to /etc during package upgrades. It tracks file metadata that revison control systems do not normally support, but that is important for /etc, such as the permissions of /etc/shadow. It's quite modular and configurable, while also being simple to use if you understand the basics of working with revision control.`

Under Debian Lenny :

Install :

`apt-get install etckeeper`

Set ut the git repository (git is the default repository system, see /etc/etckeeper/etckeeper.conf if you want to change that) :

`cd /etc
etckeeper init
etckeeper commit "initial import"`

We're done with the initial setup..

If you install a package through apt-get or aptitude, changes will be automatically commited.

If you made changes to files under /etc, commit them by doing :

`git commit -a`

Also, you can install trac-git, which provides a user friendly web interface to browse your git repository.
