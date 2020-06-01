---
date: 2019-02-18
title: "An easy Pastebin service with command line client"
---

Let me introduce you to [Hastebin](https://hastebin.com/about.md).

It is a simple pastebin service that you can self-host.

The server bits are available at [https://github.com/seejohnrun/haste-server](https://github.com/seejohnrun/haste-server).

I personally use this Docker container: [https://hub.docker.com/r/mkodockx/docker-pastebin](https://hub.docker.com/r/mkodockx/docker-pastebin)

The **awesome** part is that a command line client exists!

On your Linux workstation, just do a simple `gem install haste`.

Then create an alias in your `.bashrc` or `.zshrc` config: `alias pastebin="HASTE_SERVER=https://your-haste-server.com haste"`.

Then after restarting your session or sourcing your config file, you can start pasting from the CLI:

```
echo blah | pastebin
https://your-haste-server.com/ecikubicuk
```

The client sources are available at [https://github.com/seejohnrun/haste-client](https://github.com/seejohnrun/haste-client)