---
date: 2007-01-10
title: "Subversion- automating svn-keywords"
---






categories:  
- Howto  
- Linux  
- Versioning

We'll see here how you can automate the addition of SVN keywords to your newly added or imported file in your Subversion repository.

**Changes are being made on the client computer, NOT the repository. It means you need to apply the following to every computer accessing the SVN repository.**


For files already in the repository, you will need to manually set the keywords this way:

	svn propset svn:keywords "Id" file.sh

To set scripts as executable: 

	svn propset svn:executable ON file.sh

### 1. Enabling auto properties

Edit ~/.subversion/config

Under the miscellany section, set enable-auto-props to yes 

	[miscellany]
	enable-auto-props = yes

Then, you should find an auto-prop section :

	[auto-props]
	### The format of the entries is:
	###   file-name-pattern = propname[=value][;propname[=value]...]
	### The file-name-pattern can contain wildcards (such as '*' and
	### '?').  All entries which match will be applied to the file.
	### Note that auto-props functionality must be enabled, which
	### is typically done by setting the 'enable-auto-props' option.
	# *.c = svn:eol-style=native
	# *.cpp = svn:eol-style=native
	# *.h = svn:eol-style=native
	# *.dsp = svn:eol-style=CRLF
	# *.dsw = svn:eol-style=CRLF
	# *.sh = svn:eol-style=native;svn:executable
	# *.txt = svn:eol-style=native
	# *.png = svn:mime-type=image/png
	# *.jpg = svn:mime-type=image/jpeg
	# Makefile = svn:eol-style=native

If I wanted to have the "Id" keyword automatically added to *.sh files, I'd uncomment the *.sh line and add the keyword values, so it'd look like this:

	*.sh = svn:eol-style=native;svn:executable;svn:keywords=Id

### 2. Editing your files

Just put somewhere in your bash script the "$Id$" keyword, add and commit :

	$ vi test.sh
	#!/bin/bash
	# SVN : $Id$
	echo "This is a test"

Add and commit : 

	$ svn add test.sh
	A         test.sh
	$ svn commit -m "Testing auto keywords"
	Sending        test.sh
	Transmitting file data .vi 
	Committed revision 30.

Once commited, the file would look like this :

	#!/bin/bash
	# SVN : $Id: test.sh 30 2007-01-10 23:34:59Z user $
	echo "This is a test"

### 3. Verifying the properties of a file

If you want to check out the properties set on a file, just do:

	$ svn proplist test.sh 
	Properties on 'test.sh':
	  svn:executable
	  svn:keywords
	  svn:eol-style

If you want to check out the keywords set on that file:

	$ svn proplist test.sh -v
	Properties on 'test.sh':
	  svn:executable : *
	  svn:keywords : Id
	  svn:eol-style : native
