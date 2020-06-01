---
date: 2007-02-19
title: "Users/groups management under RHEL"
---







categories:
- RHCE


useradd [options] account
usermod [options] account
userdel [-r] account (-r would delete the home directory)



Default values when creating a user stored under /etc/default/useradd
`# useradd defaults file
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel`

/etc/skel contains the skeleton of the files in the home directory of any newly created account
 
/etc/login.defs  file defines the site-specific configuration for the shadow password suite.

Disable an account : passwd -l account
Enable an account : passwd -u account

groupadd [options] group
groupmod [options] group
groupdel [options] group

chfn : change finger info
id : show your user and group ID's
groups : displays groups which you belong to
finger account : get info about an account

pwck : verify integrity of password file
grpck : verify integrity of group file

pwconv : creates  shadow  from  passwd  and  an optionally existing shadow.
pwunconv : creates passwd from passwd and shadow and  then  removes  shadow.
grpconv : creates  gshadow  from  group and an optionally existing gshadow.
grpunconv : creates group from group and gshadow and then removes gshadow.

