---
date: 2016-02-07
title: "Malformed header from script cgi"
---

If you want to run a bash script as a CGI and get this message:

	[Sun Feb 07 19:51:59.424071 2016] [cgi:error] [pid 4265] [client x.x.x.x:50952] malformed header from script 'weather.sh': Bad header: Searching via name..
   
Make sure to put an additional echo in your script after the content type

	#!/bin/bash
	echo "Content-type: text/html"
	echo
	your-command
