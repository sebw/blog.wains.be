---
date: 2021-03-03
title: "Fix crackling sound in Libvirt VM with PulseAudio"
---

So I run a Fedora virtual machine on a Libvirt host running in my garage.

I use SPICE and virt-viewer from my laptop in the office to connect to the VM.

Despite the cabled gigabit link, SPICE performances are not optimal.

While I can live with poor full screen video performances in SPICE, I can't stand crackling audio.

The crackling audio was particularly bad when scrolling in a webpage in a browser or any kind of zooming on pictures.


## Receiving end / PulseAudio Server / Audio output / The Laptop

Install the `paprefs` package.

Start `paprefs`.

Navigate to the "Network Server" tab".

Tick "Enable network access to local sound devices". I ticked the authentication box as I have strict firewall rules between the source and destination.

![](https://blog.wains.be/images/pulseaudio/paprefs.png)

At the command line kill PulseAudio with `pulseaudio -k`.

If PulseAudio doesn't restart automatically (it should) you can run `pulseaudio --start`.

Open the TCP/4713 port used for receiving the audio.

```
firewall-cmd --add-port=4713/tcp --permanent
firewall-cmd --reload
```

## Sending end / Client / Audio input / the VM

Edit `/etc/pulse/default.pa`.

At the end of the file add the following line:

```
load-module module-tunnel-sink-new server=laptop.example.org sink_name=Remote channels=2 rate=44100
```

Replace `laptop.example.org` by the hostname of IP of your server.

Restart PulseAudio with `pulseaudio -k`.

If PulseAudio doesn't restart automatically (it should) you can run `pulseaudio --start`.

Start PulseAudio volume control with `pavucontrol`.

Start your music player.

You should be able to send your audio through the tunnel:

![](https://blog.wains.be/images/pulseaudio/pavucontrol1.png)

You can also control the volume of the output:

![](https://blog.wains.be/images/pulseaudio/pavucontrol2.png)

I now enjoy a crackle free audio experience.