---
date: 2006-12-18
title: "Squid - log Google and other search engines related queries"
---







categories:
- Linux
- Proxy
- Security


By default, the queries sent to google and other search engines are not logged into Squid proxy logs. This is meant to protect the user's privacy.

See below how to enable queries logging..



Add this to /etc/squid/squid.conf

`strip_query_terms off`

As a result, you'd get this in the logs :
`1166463218.353   1285 192.168.1.10 TCP_MISS/200 6429 GET http://www.google.com/search?hl=en&q=s%C3%A9bastien+wains&btnG=Google+Search - DIRECT/64.233.161.104 text/html`

Instead of this :

`1166463218.353   1285 192.168.1.10 TCP_MISS/200 6429 GET http://www.google.com - DIRECT/64.233.161.104 text/html`
