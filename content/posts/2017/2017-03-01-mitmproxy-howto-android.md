---
date: 2017-03-01
title: "MITM proxy howto and Android"
---

Start mitmproxy:

`./mitmproxy --host --follow`

Configure your Android device to use the proxy:

- go in Wi-Fi settings
- list the networks
- long press on the current network
- specify the proxy host and port

In a browser on your Android device go to http://mitm.it (it is not an actual website, but the proxy that is displaying this page).

Install and accept the certificate to validate websites

Start apps or browse the web, you should see trafic in mitmproxy
