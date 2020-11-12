---
date: 2020-11-12
title: "apcupsd custom script doesn't work"
---

By default on a Fedora system running SELinux in enforcing mode, custom scripts won't be able to make any connection through curl, wget or else.

I changed `/etc/apcupsd/onbattery` to notify me on my Gotify instance, but it was not working when unplugging the UPS.

A quick look in `journalctl` gives us a pretty good hint:

```
Nov 12 22:04:47 yo.example.org python3[13500]: SELinux is preventing curl from name_connect access on the tcp_socket port 443.

                                                *****  Plugin catchall_boolean (89.3 confidence) suggests   ******************

                                                If you want to allow nis to enabled
                                                Then you must tell SELinux about this by enabling the 'nis_enabled' boolean.

                                                Do
                                                setsebool -P nis_enabled 1

                                                *****  Plugin catchall (11.6 confidence) suggests   **************************

                                                If you believe that curl should be allowed name_connect access on the port 443 tcp_socket by default.
                                                Then you should report this as a bug.
                                                You can generate a local policy module to allow this access.
                                                Do
                                                allow this access for now by executing:
                                                # ausearch -c 'curl' --raw | audit2allow -M my-curl
                                                # semodule -X 300 -i my-curl.pp
```

Apply the boolean:

```bash
setsebool -P nis_enabled 1
```

Now if you unplug your UPS, the curl call should work.