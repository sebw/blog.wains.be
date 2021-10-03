---
date: 2021-10-03
title: "Enable virtual camera in OBS Studio on Fedora"
---

By default on Fedora, when you install the package `obs-studio` you might not see the "Start virtual camera" button.

The reason is that OBS is expecting a v4l2loopback module loaded.

In order to do so, install the package "v4l2loopback":

`sudo dnf install v4l2loopback`

Then insert the module:

`sudo modprobe v4l2loopback`

Now, start OBS Studio and you should see the "Start virtual camera" menu in the lower right corner.

The virtual camera allows you to inject pictures, logs, text, etc. in your camera stream.

When in Zoom, Google Meet, Webex or others, simply choose the virtual camera as your video input, and your audience should whatever you added in OBS Studio.

Have fun!