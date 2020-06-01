---
date: 2011-03-13
title: "Importing certificates on Android (CA and client)"
---







categories:
- Android


_Tested on my HTC Hero running Android 2.2.1_

They do not make it terribly obvious, so I believe this is worth a post.

Android will not import CA cert in the PEM format, you'll get a "no certificate to install" message at some point.

You actually have to export a P12 certificate containing the client certificate and the CA.

Use this command :
`openssl pkcs12 -export -in clientcert.pem -inkey clientcert.key -certfile cacert.pem -name "VPN" -out clientcert.p12`

Drop the resulting file (clientcert.p12) at the root of your sdcard.

Go under Settings > Location & Security > Install from SD card (under the section "Credential storage").

After a few questions, you're ready to go and you can proceed with the configuration of your Wi-Fi or VPN client (in my case WPA Enterprise Wi-Fi and OpenVPN).
