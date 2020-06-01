---
date: 2011-07-06
title: "Large files uploading fail with Apache + PHP + APC"
---







categories:
- Apache


We had one quite interesting problem at work.

We had a Drupal site where we couldn't upload files larger than 32 MB, while having in php.ini :

`upload_max_filesize = 200 MB
post_max_size = 200M`

After disabling APC, we could upload larger files.

It turns out, it seems changing the following in apc.ini

`apc.rfc1867_freq=0`

to 

`apc.rfc1867_freq=100k`

fixed the problem.

Doc : [http://www.php.net/manual/en/apc.configuration.php#ini.apc.rfc1867-freq](http://www.php.net/manual/en/apc.configuration.php#ini.apc.rfc1867-freq)


    
    <code>apc.rfc1867_freq string
    
    The frequency that updates should be made to the user cache entry for upload progress. This can take the form of a percentage of the total file size or a size in bytes optionally suffixed with "k", "m", or "g" for kilobytes, megabytes, or gigabytes respectively (case insensitive). A setting of 0 updates as often as possible, which may cause slower uploads.</code>



I'm pretty sure this should not be related, as I have apc.rfc1867=0 in apc.ini. 

If someone has a clue, drop me a line :-)

