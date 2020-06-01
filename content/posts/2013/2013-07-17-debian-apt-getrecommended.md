---
date: 2013-07-17
title: "Debian tell apt-get not to install recommended packages"
---
Date: 2013-07-17
Tags: Linux, Debian

In /etc/apt/apt.conf

	APT::Install-Recommends "0";
