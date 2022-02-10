---
date: 2022-02-10
title: "Managing Thundebolt security on Fedora"
---

I have a Lenovo t14s and a Dell WD19TB Thunderbolt Dock.

The thunderbolt security level is set to user authorized in the BIOS.

You need to use `boltctl` to authorize devices.

If you want to authorize the docking you can use:

```bash
boltctl list
# take note of the uuid of your device
boltctl authorize XXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
```

Authorizing won't remember your docking if you unplug and replug it. You would have to authorize again.

If you want to store the docking in the database (AKA remembering it), you need to enroll instead:

```bash
boltctl enroll XXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
```

For some reason, enrolling after authorizing was not really doing anything. If I unplugged and replugged the docking, my keyboard, screen, etc would not show up.

I had to forget the device before re-enrolling:

```bash
boltctl remove XXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
boltctl enroll XXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
```

Now my docking is recognized immediately and all my devices show up.

If you want to troubleshoot the bolt service:

```bash
$ journalctl -u bolt.service -f

Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] connected: connected (/sys/devices/pci0000:00/0000:00:1c.4/0000:04:00.0/0000:05:00.0/0000:06:00.0/domain0/0-0/0-1)
Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] auto-auth: authmode: enabled, policy: auto, iommu: no -> ok
Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] auto-auth: security: user mode, key: no -> ok
Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] authorize: authorization prepared for 'user' level
Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] udev: device changed: authorizing -> authorizing
Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] authorize: finished: ok (status: authorized, flags: 0)
Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] auto-auth: authorization successful
Feb 10 00:58:10 ws.wains.lan boltd[1112]: [0024916c-065f-WD19TB Thunderbolt Dock    ] udev: device changed: authorized -> authorized
```