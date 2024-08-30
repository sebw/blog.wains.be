---
date: 2021-10-03
title: "Enable virtual camera in OBS Studio on Fedora"
---

_TESTED ON FEDORA 34 AND 40. PLEASE REPORT SUCCESS OR FAILURE FOR YOUR FEDORA VERSION IN THE COMMENTS_

By default on Fedora, when you install the package `obs-studio` you might not see the "Start virtual camera" button.

The reason is that OBS is expecting a v4l2loopback module to be loaded.

## Installation

In order to do so, you will need to enable rpmfusion repositories for your version of Fedora and install the package "v4l2loopback":

```bash
VERSION=`lsb_release -r -s`
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-${VERSION}.noarch.rpm
```

The above code requires lsb_release to be installed, or just set the VERSION manually if you don't want to install the tool.

```bash
sudo dnf install v4l2loopback kmod-v4l2loopback
```

Then insert the module:

```bash
sudo modprobe v4l2loopback devices=1 max_buffers=2 exclusive_caps=1 card_label="VirtualCam"
```

If modprobe fails try this before rerunning the `modprobe` command:

```bash
sudo depmod
```

If installing the package pulled a new kernel version and modprobe still fails to locate the module, you might need to reboot your system.


## OBS Studio

Now, start OBS Studio and you should see the "Start virtual camera" menu in the lower right corner.

The virtual camera allows you to inject pictures, logos, text, video streams (using VLC), etc. in your original webcam stream.

When in Zoom, Google Meet, Webex or others, simply choose the virtual camera as your video input device, and your audience should whatever you added in OBS Studio.

## Virtual camera configuration

You can configure some settings for your new virtual camera with `v4l2loopback-ctl`.

Have fun!
