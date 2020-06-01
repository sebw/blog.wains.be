---
date: 2007-02-21
title: "RHEL package management"
---







categories:
- RHCE


Compilation of most common commands dealing with package management.



**up2date**

Install
`up2date -i package`

Show packages groups
`up2date --show-groups`

Install a package group
`up2date -i @GROUP_NAME (e.g. : up2date -i @GNOME Desktop Environment)`

up2date sources specified under /etc/sysconfig/rhn/sources

**yum**

Install
`yum install package`

Get info
`yum info package`

Remove 
`yum erase package`

Check if update available
`yum check-update`

Find package providing the file
`yum whatprovides /some/file`

Clean all yum caches
`yum clean all`

Yum sources specified under /etc/yum.repos.d/

**rpm**

Install
`rpm -ihv package.rpm`

Reinstall an already installed package
`rpm -ihv replacepkgs package.rpm`

Update
`rpm -Uhv package.rpm`

Remove (--nodeps will break dependencies)
`rpm -e package (--nodeps)`

Find dependencies or** find which package a file belongs to**
`rpm -q --redhatprovides missing-dep-file.so.2`

Find which package owns a file
`rpm -qf /usb/bin/vim`

Get info. use -qip if getting info from a rpm package
`rpm -qi(p) package(.rpm)`

List packages installed
`rpm -qa`

Sort rpms by installation date
`rpm -qa --last`
