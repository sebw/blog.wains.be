---
date: 2006-10-05
title: "Prohibit direct linking to your site"
---







categories:
- Apache
- Howto
- Linux
- Security


Sick of people direct linking ( [http://en.wikipedia.org/wiki/Direct_linking](http://en.wikipedia.org/wiki/Direct_linking) ) images from your site ?

Use this under an .htaccess file or your apache configuration (modrewrite needed)

This one would display the image owner.jpg :
`RewriteEngine On
RewriteCond   %{HTTP_REFERER} ^.*myspace.com.*$ [NC]
RewriteRule   .*.(gif|jpg|jpeg|swf|png)$ http://www.site.be/images/owner.jpg [NC]`

This one would block direct linking, at all :
`RewriteEngine On
RewriteCond   %{HTTP_REFERER} ^.*myspace.com.*$ [NC]
ReWriteRule   .*.(gif|png|jpg|jpeg|swf)$     - [F]`
