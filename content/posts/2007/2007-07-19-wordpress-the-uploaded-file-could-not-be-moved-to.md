---
date: 2007-07-19
title: "Wordpress - The uploaded file could not be moved to ."
---







categories:
- Security


If you get this message when trying to upload a file into wordpress admin area, this may mean that :

- the webserver doesn't have the write permissions on the upload directory
- safe mode is enabled on the webserver

The easiest fix is to edit the value "safe_mode" to off in /etc/php.ini
**YOU MAY NOT WANT TO DO THAT, THIS IS NOT SAFE**

Read on..



**What is safe mode ?**

See [http://be.php.net/features.safe-mode](http://be.php.net/features.safe-mode)

**Work-around to upload files with safe mode on :**

See [http://tiny3d.com/?p=35](http://tiny3d.com/?p=35)

Here's the copy in case the link goes down

Wordpress - File Upload Problems
Saturday 2nd September 2006, 0:49

When configuring Wordpress to support file uploads I ran into a tricky issue. The server responded with:

`    The uploaded file could not be moved to `.

The message above is displayed if there is a problem uploading or using the uploaded file, this is normally associated with security issues, such as the user that the webserver is running under doesn’t have write permission in the folder.

There is a thread on the wordpress site that discuss this issue. The most common cause for this problem is invalid directory permissions.

My problem however was not solved by changing the permissions of the folders. No matter what I tried it refused to work.

After turning to google I found out that this type of problems may also be caused by PHP being configured to run in SAFE MODE safe_mod=On in php.ini) which has the same effect as incorrect permissions. Php will refuse to read files that are not owned by the same user as the script being executed.

So by turning off safe mode (safe_mode=Off) the file uploads worked without problems… but personally I prefer running with safe mode enabled. So I needed to find another solution to allow file uploads even though safe mod is enabled..

It so happens that there is a related configuration option in php.ini. By setting safe_mode_gid=On safe mode checks the files GID (group) instead of the UID (user). When combining this with a sticky flag on the uploads folder (and it’s underlying year-folders if you have that option is enabled in wordpress) we can get file uploads to work properly even though safe mode is still enabled. Here is a list of what to do:

`        * Enable safe_mode_gid=On in php.ini
        * chmod 7777 wp-content/uploads
        * If the organisation option is enabled in wordpress, create one or more year folders and set chmod 7777 on them aswell.
        * Restart the webserver so that the php.ini changes take effect.
        * Try uploading a file.`



Do not thank me, thank the original author at [http://tiny3d.com](http://tiny3d.com)
