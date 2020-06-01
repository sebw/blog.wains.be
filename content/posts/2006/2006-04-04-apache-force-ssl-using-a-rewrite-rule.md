---
date: 2006-04-04
title: "Apache- force SSL using a rewrite rule"
---

**This rewrite rule will redirect the requests to https (SSL) :**

```
RewriteEngine on
RewriteCond %{SERVER_PORT} !^443$
RewriteRule ^.*$ https://%{SERVER_NAME}%{REQUEST_URI} [L,R]
```

The module mod_rewrite is needed.

Something like "LoadModule rewrite_module modules/mod_rewrite.so" in your apache config should enable it.
