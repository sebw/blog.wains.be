---
date: 2019-03-16
title: "Gotify- a self-hosted push notification app"
---

[Project page](https://gotify.net/)

[Source code](https://github.com/gotify/server)

Gotify is:

- a simple server for sending and receiving messages
- self-hosted
- free and open source under MIT license
- a simple [API](https://gotify.net/api-docs)
- a simple GUI
- written in Go
- building docker images at every release

![Gotify](https://blog.wains.be/images/gotify.png)

It comes with a nice Android application available on the [Play Store](https://play.google.com/store/apps/details?id=com.github.gotify) and [F-Droid](https://gotify.net/img/fdroid.png), that listens for events over a websocket.

I have been using it for about 10-12 days now, without noticeable impact on battery.

EDIT 03/2020: still using the Android app, and still no battery impact.

It is easy to send messages to Gotify:

```
$ curl -X POST "https://notifications.example.org/message?token=<apptoken>" -F "title=my title" -F "message=my message"
```

```
$ http -f POST "https://notifications.example.org/message?token=<apptoken>" title="my title" message="my message"
```

The token can be generated from the GUI or API.

When receiving a message, your Android will immediately notify you of any message.

The developers are very active, looks like a promising project!

#### Alternative

Up until now, I was using [Simplepush](https://simplepush.io), which is basically the same concept but sends messages to Simplepush servers. 

Also the Android app is not open source

Being not self-hosted you don't control your data, and the developer can turn off the service overnight.
