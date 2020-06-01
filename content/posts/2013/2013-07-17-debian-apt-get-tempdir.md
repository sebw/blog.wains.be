---
date: 2013-07-17
title: "Debian apt-get change temporary directory"
---
Date: 2013-07-17
Tags: Linux, Debian

Under Debian, if /tmp is a partition and you set it as noexec, you might need to do this to avoid issues with apt-get.

*Create /etc/apt/apt.conf.d/50tmp*

	APT
	{
	  ExtractTemplates
	  {
		TempDir "/var/tmp";
	  };
	};
