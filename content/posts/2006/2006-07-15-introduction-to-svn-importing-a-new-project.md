---
date: 2006-07-15
title: "Introduction to SVN - importing a new project"
---

This post is aimed to those (like me) who can easily forget the SVN syntax :

**Create a new repository :**
`$ svnadmin create --fs-type fsfs /home/user/svn`

**Suppose you have an existing project you wish to import in SVN :**
`$ svn import /path/to/project/ file:///home/user/svn/project -m 'Initial import'`

**Checking out your project :**
`$ svn checkout file:///home/user/svn/project /home/user/dev/project`

A version controlled copy of the project is now available under /home/user/dev/project/

You can remove /path/to/project/ as soon as you are sure the version controlled copy is there.

You can start working on your project under /home/user/dev/project and issue the following to commit changes to the SVN repository :
`$ svn commit -m 'Your comment here' `
