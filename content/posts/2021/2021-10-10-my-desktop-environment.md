---
date: 2021-10-10
title: "★★ My Linux desktop environment (2021 update)"
---

## Moving from i3wm to bspwm

In December 2019, I wrote about my [Linux desktop environment running i3wm](https://blog.wains.be/2019/2019-12-11-my-linux-desktop-environment/).

In May 2021, I changed role at Red Hat, requiring me to present and share my screen a lot more than when I was a consultant.

I started looking at how other tiling window managers were handling things and decided to give [bspwm](https://github.com/baskerville/bspwm) a try.

After some tests I made the move because it is indeed easier to manage windows, I really liked the "client" (called `bspc`) to interact with bspwm but also it felt slightly lighter.

I'm going to go over my current desktop environment and workflows in this post.

## Disclaimer

This desktop environment is probably not for you. I have my own workflows and have no pretention that this is the best way to organize your environment. At the very least it can give some ideas.

## Hardware

Lenovo t14s with 32GB of RAM and 1TB NVME.

## Operating System

Fedora i3 spin.

I use the i3 spin to bootstrap my bspwm installation (using Ansible playbooks). This means I don't pull all the Gnome dependencies and the i3 spin brings LightDM, which I like better.

## How does it look like today?

![](https://img.wains.be/images/aafdb228cf32f98475a0f7fd2b6333ff.png)

For some reason, most icons in the tray are hidden when taking a screenshot with `flameshot`.

### Bar

I use Polybar and used [https://github.com/adi1090x/polybar-themes](https://github.com/adi1090x/polybar-themes) for inspirations.

I found most theme overwhelming and I was looking for something lighter. I removed most of the modules and only kept the CPU state, date, desktops and tray.

### Nord Theme

I try to apply [Nord Theme](https://www.nordtheme.com/) to pretty much everything.

### bspwm doesn't have a scratchpad

This was kind of a blocker for me.

It turns out I created 12 desktops and use the 12th desktop as scratchpad. I reproduced the key shortcut I was using (win+z) to send the window to the scratchpad/desktop 12.

In `$HOME/.config/sxhkd/sxhkdrc`:

```bash
super + z
    bspc node focused -t floating; bspc node -d '^12'
```

To retrieve windows I just alt-tab and choose the window I want back and press again `win+z`:

```bash
alt + Tab
        rofi -show window -window-thumbnail
```

If you want to accept selection with `win+z` in rofi, add the following in rofi's configuration: `kb-accept-alt: "Super+z";`

## On using virtual machines

In 2019 I was running my desktop environment inside virtual machines. I stopped doing that and now install Fedora i3 spin directly on the laptop, and then install the bspwm and other packages.

## Storing data

Unchanged since 2019, I store everything of importance in a self-hosted Seafile.

My configurations are stored in git and managed with Stow.

## Taking long lived notes

Joplin turned out to be a mess when using multiple devices. I actually lost some content (pictures).

I moved to [Zim](https://zim-wiki.org/) and store all my notes in a Git repository.

Zim comes with plugins, one of which being version control which can automatically commit your changes regularly, or upon exiting Zim.

A git hook automatically pushes committed changes to the repository.

## Taking short lived notes

I still use Xpad when I need to quickly save some info.

