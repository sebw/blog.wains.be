---
date: 2007-02-19
title: "tar, gzip, bzip2"
---







categories:
- RHCE


Overview of the most common commands when it comes to compression...



**Tar**

Create a tar file :
`tar -cpf target.tar source_folder/`

Create a tar.gz file :
`tar -cpzf target.tar.gz source_folder/`

Create a tar.bz file :
`tar -cpjf target.tar.bz2 source_folder/`

`-c : create
-p : preserve permissions
-z : gzip
-j : bzip
-f : file
-x : extract
-v : verbose
-t : test`

Extract foo.tar :
`tar -xvf foo.tar`

Extract gzipped foo.tar.gz :
`tar -xvzf foo.tar.gz`

Extract bzipped foo.tar.bz2 after changing directory (must exist) to bar :
`tar -xvjf foo.tar.bz2 -C bar/`

Extract the file blah.txt from foo.tar.bz2 :
`tar -xvzf foo.tar.gz blah.txt`

Test extraction :
`tar -tvzf foo.tar.gz`

**gzip**

Extract file :
`gzip -d file.gz`

Compress file :
`gzip file`

Getting info from the zip file :
`gzip -l foo.tar.gz
         compressed        uncompressed  ratio uncompressed_name
                176               10240  98.5% foo.tar`

**bzip**

Same as gzip

Best compression :
`bzip2 -9 file`
