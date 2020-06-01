---
date: 2016-01-15
title: "Apache rewrite rule with original URL containing parameters"
---
So let's say you want to redirect [http://example.org?id=2392](http://example.org?id=2392) to [http://example.org?id=256](http://example.org?id=256)

You need to use the `%{QUERY_STRING}` option under RewriteCond like this:

```
RewriteCond %{QUERY_STRING} id=2392
RewriteRule ^/index.php http://example.org/index.php?id=256 [L,R=permanent]
```
