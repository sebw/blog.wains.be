---
date: 2007-05-02
title: "VMware server on Ubuntu 7.04 the easy way"
---







categories:
- Howto
- Linux


You would find howtos here and there about installation of vmware server the manual way.

There's a much easier way, as it is available in the commercial repository of Canonical

Add this line to /etc/apt/sources.list :
`deb http://archive.canonical.com/ubuntu feisty-commercial main`

From synaptic, install vmware-server, it will install its dependencies along.

Obtain a serial number at [http://register.vmware.com/content/registration.html](http://register.vmware.com/content/registration.html)

As soon as it is installed, you can access the vmware console from the Applications menu > System tools > VMware server console
