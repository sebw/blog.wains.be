---
date: 2013-07-06
title: "Simple HTTP server with Python		"
---

Simply run this from the command line and it will server the content of the current directory:

```bash
python -m SimpleHTTPServer 80
```

I recommend setting an alias for that and make sure that the port is always open from the internet. It makes it very easy to share stuff with friends temporarily.

## Python 3

SimpleHTTPServer is gone in Python 3.

You can now use:

```bash
python3 -m http.server 8000
```
