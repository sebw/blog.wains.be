---
date: 2013-07-17
title: "Python iteration through dictionnaries"
---
Date: 2013-07-17
Tags: python

We create a dictionary called "d":

	d = {'site1': {'servername': 'test.org', 'serveralias': 'www.test.org'}}

We parse it with iteritems():

	for key, value in d.iteritems():
	    print key
	    print value['servername']
