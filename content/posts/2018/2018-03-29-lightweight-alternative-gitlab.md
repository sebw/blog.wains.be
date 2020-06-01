---
date: 2018-03-29
title: "Gogs - a lightweight alternative to Gitlab"
---

If you need to self-hosted and lightweight Github or Gitlab alternative, I suggest you give [Gogs](https://gogs.io/) a try.

Gogs provides Docker images here: [https://hub.docker.com/r/gogs/gogs/](https://hub.docker.com/r/gogs/gogs/)

Start the image, put a reverse proxy in front, secure the whole thing with Let's Encrypt, and you should be up and running in a couple of minutes.

If you're hosting a couple of repos and a few users, you can use a sqlite backend.

You can also use Postgres or Mysql databases.

There's a fork of Gogs called Gitea: [https://gitea.io](https://gitea.io)

I have the feeling Gitea is not actively maintained anymore. For example the demo site has been down for a while now.