---
date: 2017-05-14
title: "Fedora- replacing firewalld by iptables but firewalld always starts instead of iptables"
---

I've been using iptables for more than 10 years and I don't really like firewalld approach.

When I installed my Fedora 25 workstation, I installed a GUI-less Fedora Server edition, and installed i3wm on top of that.

One of the steps has been to disable firewalld and replace it by iptables, see doc [here](https://docs.fedoraproject.org/en-US/Fedora/19/html/Security_Guide/sec-Disabling_firewalld.html). The doc is outdated (for Fedora 19) but still relevant.

After every reboot, firewalld always started in favor of iptables.

It is due to a dependency on rolekit.

See here:

[https://bugzilla.redhat.com/show_bug.cgi?id=1301258](https://bugzilla.redhat.com/show_bug.cgi?id=1301258)
