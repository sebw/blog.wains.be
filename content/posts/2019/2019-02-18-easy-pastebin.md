---
date: 2019-02-18
title: "An easy Pastebin-like service with a command line client"
---

Let me introduce you to [Hastebin](https://hastebin.com/about.md).

It is a simple pastebin service that you can self-host.

## Server 

The server bits are available at [https://github.com/seejohnrun/haste-server](https://github.com/seejohnrun/haste-server).

I personally use this Docker container: [https://hub.docker.com/r/mkodockx/docker-pastebin](https://hub.docker.com/r/mkodockx/docker-pastebin)

## Client

The **awesome** part is that you can paste from the command line!

You have two choices: using **gem** or a simple **bash alias** that doesn't require you to install anything (besides `curl`).

### Using Gem

```bash
gem install haste
```

Create an alias in your `.bashrc` or `.zshrc` config:

```bash
alias pastebin="HASTE_SERVER=https://your-haste-server.com haste"
```

Then after restarting your session or sourcing your config file.

[Client sources](https://github.com/seejohnrun/haste-client)

### Using a bash alias

```bash
pastebin() { a=$(cat); curl -X POST -s -d "$a" https://your-haste-server.com/documents | awk -F '"' '{print "https://your-haste-server.com/"$4}'; }
```

## Demo

```
echo blah | pastebin
https://your-haste-server.com/ecikubicuk
```