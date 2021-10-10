---
date: 2021-10-10
title: "★★ My Linux desktop environment (2021 update)"
---

## Moving from i3wm to bspwm

In December 2019, I wrote about my [Linux desktop environment running i3wm](https://blog.wains.be/2019/2019-12-11-my-linux-desktop-environment/).

In May 2021, I changed role at Red Hat, requiring me to present and share my screen a lot more than when I was a consultant.

I started looking at how other tiling window managers were handling things and decided to give [bspwm](https://github.com/baskerville/bspwm) a try.

After some tests I made the move because it is indeed easier to manage windows, I really liked the "client" (called `bspc`) to interact with bspwm but it also felt slightly lighter.

In i3wm, if you open 5 terminals in the same desktop you get 5 "columns".

In bspwm, it will look like this:

![d5b858eae2a0e2fcf200907d4d003065.png](https://img.wains.be/images/d5b858eae2a0e2fcf200907d4d003065.png)

This is one of the biggest improvement for me.

The second biggest improvement is how you can easily reorganize and resize your windows:

[![d9f2ee6064fa9ee7cf47604b9d42b0e3.gif](https://img.wains.be/images/d9f2ee6064fa9ee7cf47604b9d42b0e3.gif)](https://img.wains.be/image/bXQG)

I'm going to go over my current desktop environment and workflows in this post.

## Disclaimer

This desktop environment is probably not for you. I have my own workflows and have no pretention that this is the best way to organize your environment. At the very least it can give some ideas.

## Hardware

Lenovo t14s with 32GB of RAM and 1TB NVME.

## Operating System

Fedora i3 spin.

I use the i3 spin to bootstrap my bspwm installation (using Ansible playbooks). This means I don't pull all the Gnome dependencies and the i3 spin brings LightDM, which I like better.

### Bar

I use Polybar and used [https://github.com/adi1090x/polybar-themes](https://github.com/adi1090x/polybar-themes) for inspirations.

I found most theme overwhelming and I was looking for something lighter. I removed most of the modules and only kept the CPU state, date, desktops and tray.

### Nord Theme

I use [Nord Theme](https://www.nordtheme.com/) everywhere.

### bspwm doesn't have a scratchpad

This was initially a kind of blocker for me.

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

### Resizing windows makes more sense in i3

By default, in bspwm you need two shortcuts to resize a window. One to make the window larger, one to make the window smaller. But `bspc` is powerful so you can make it happen.

I'm now using this script to do everything from a single command:

```bash
#!/bin/bash

# Used in bspwm to resize with a single key shortcut

wid=$(xdotool getactivewindow)
wininfo=$(xwininfo -id "$wid")
width=$(echo "$wininfo" | awk '/Width/ {print $2}')
height=$(echo "$wininfo" | awk '/Height/ {print $2}')
case $1 in
	left)
		# bspc node @east -r -40 || bspc node @west -r -40
		bspc node -z left -40 0
		if [[ $width == "$(xwininfo -id "$wid" | \
				awk '/Width/ {print $2}')" ]]; then
			bspc node -z right -40 0
		fi
		;;
	down)
		# bspc node @south -r +35 || bspc node @north -r +35
		bspc node -z bottom 0 +35
		if [[ $height == "$(xwininfo -id "$wid" | \
				awk '/Height/ {print $2}')" ]]; then
			bspc node -z top 0 +35
		fi
		;;
	up)
		# bspc node @north -r -35 || bspc node @south -r -35
		bspc node -z top 0 -35
		if [[ $height == "$(xwininfo -id "$wid" | \
				awk '/Height/ {print $2}')" ]]; then
			bspc node -z bottom 0 -35
		fi
		;;
	right)
		# bspc node @west -r +40 || bspc node @east -r +40
		bspc node -z right +40 0
		if [[ $width == "$(xwininfo -id "$wid" | \
				awk '/Width/ {print $2}')" ]]; then
			bspc node -z left +40 0
		fi
		;;
esac
```

In my `sxhkdrc` file:

```bash
ctrl + super + {Left,Down,Up,Right}
    $HOME/.config/bspwm/resize.sh {left,down,up,right}
```

### Keyboard shortcut helper

Given the number of keyboard shortcuts, it can happen I forget how to do something.

I use [sxhkd-helper-menu](https://github.com/fiskhest/sxhkd-helper-menu) with `rofi` to help me when I forget a shortcut. I use the `win+h` combo.

## On using virtual machines

In 2019 I was running my desktop environment inside virtual machines. I stopped doing that and now install Fedora i3 spin directly on the laptop, and then install the bspwm and other packages.

## Storing data

Unchanged since 2019, I store everything of importance in a self-hosted Seafile.

My configurations are stored in git and managed with Stow.

## Taking long lived notes

Joplin turned out to be a mess when syncing using multiple devices. I actually lost some content (screenshots attached to the notes).

I moved to [Zim](https://zim-wiki.org/) and store all my notes in a Git repository.

Zim comes with plugins, one of which being version control which can automatically commit your changes regularly, or upon exiting Zim.

A git hook automatically pushes committed changes to the repository.

## Taking short lived notes

I still use Xpad when I need to quickly save some info.

## Terminal

Unchanged. Still using Terminator.

I gave a shot to Kitty and Alacritty but the "Watch for activity" and "Watch for silence" plugins in Terminator made me stay.

## Fonts

FiraMono. On Fedora install those packages:

```bash
mozilla-fira-fonts-common-4.202-10.fc34.noarch
mozilla-fira-mono-fonts-4.202-10.fc34.noarch
mozilla-fira-sans-fonts-4.202-10.fc34.noarch
```

## Keyboard automation

Autokey. Unchanged.

## Clipboard Manager

XFCE4 clipman. Clipit was not storing images.

## Tasks and todos

Self hosted Kanboard. Unchanged.

## Shell

ZSH + oh my ZSH + powerlevel10k.

I use the following plugins:

* git
* sudo
* k
* dirhistory
* z
* zsh-autosuggestions
* fzf
* fzf-tab
* fzf-marks

I'm a huge fan of `fzf`. I now use it for pretty much anything in the terminal.

The ZSH autocompletion uses it:

![](https://img.wains.be/images/06b04aad79db22c88ecbd4b0491da812.png)

My shell history (ctrl + r) also.

`fzf-marks` allows you to "bookmark" some folders and quickly jump to those.

This is my current `.zshrc`:

```bash
PATH=$PATH:$HOME/.local/bin

umask 077

export ZSH="${HOME}/.oh-my-zsh"
export TERM="xterm-256color"

export FZF_DEFAULT_OPTS='--height 50% --no-mouse --inline-info'
export FZF_CTRL_R_OPTS='--prompt "History > "'
export FZF_CTRL_T_OPTS='--prompt "Files > "'
export FZF_ALT_C_OPTS='--prompt "Directories > "'

ZSH_THEME="powerlevel10k/powerlevel10k"

CASE_SENSITIVE="false"
HYPHEN_INSENSITIVE="true"

POWERLEVEL9K_LEFT_PROMPT_ELEMENTS=(dir dir_writable vcs background_jobs)
POWERLEVEL9K_RIGHT_PROMPT_ELEMENTS=()

ZLE_RPROMPT_INDENT=0

POWERLEVEL9K_BACKGROUND_JOBS_VERBOSE="true"
POWERLEVEL9K_BACKGROUND_JOBS_BACKGROUND="#88C0D0"
POWERLEVEL9K_BACKGROUND_JOBS_FOREGROUND="black"
POWERLEVEL9K_BACKGROUND_JOBS_ICON="●"

POWERLEVEL9K_DIR_HOME_BACKGROUND="032"
POWERLEVEL9K_DIR_HOME_SUBFOLDER_BACKGROUND="032"
POWERLEVEL9K_DIR_DEFAULT_BACKGROUND="244"
POWERLEVEL9K_DIR_ETC_BACKGROUND="202"

POWERLEVEL9K_VCS_CLEAN_BACKGROUND="green"
POWERLEVEL9K_VCS_MODIFIED_BACKGROUND="yellow"
POWERLEVEL9K_VCS_UNTRACKED_BACKGROUND="red"

POWERLEVEL9K_STATUS_ERROR_BACKGROUND="red"
POWERLEVEL9K_STATUS_OK_BACKGROUND="green"
POWERLEVEL9K_STATUS_OK_FOREGROUND="black"

POWERLEVEL9K_DIR_PATH_SEPARATOR="%F{black} $(print $'\uE0B1') %F{black}"
POWERLEVEL9K_DIR_PATH_ABSOLUTE=false
POWERLEVEL9K_DIR_OMIT_FIRST_CHARACTER=true

POWERLEVEL9K_HOME_ICON=''
POWERLEVEL9K_HOME_SUB_ICON=''
POWERLEVEL9K_FOLDER_ICON=''
POWERLEVEL9K_ETC_ICON=''

# Shorten prompt to 2 folders
POWERLEVEL9K_SHORTEN_DIR_LENGTH=2

# Plugins
plugins=(git sudo k dirhistory z zsh-autosuggestions fzf fzf-tab fzf-marks)

# Buffer
ZSH_AUTOSUGGEST_BUFFER_MAX_SIZE=50

# Editor
export EDITOR='vim'

# Colored man pages
export PAGER='most'

# Custom alias zsh
# Execute commands
export NNN_COLORS="2136"
export NNN_TRASH=1    # trash (needs trash-cli) instead of delete
# Bookmark (b key)
export NNN_BMS="g:$HOME/git;d:$HOME/Downloads;h:$HOME;t:$HOME/.local/share/Trash/files"
# Plugins (; key) * to espace the "press enter to exit" message
export NNN_PLUG='v:imgview;f:fzcd;z:autojump'

# Start the appropriate program by just typing the filename
alias -s {yml,yaml}=ansible-playbook
alias -s {conf,adoc,md}=code
alias -s {jpg,png}=sxiv

# cd will suggest dotted folders/files. This also affects "k" that will display dotted files no matter what.
setopt globdots

# remove dups
setopt HIST_EXPIRE_DUPS_FIRST
setopt HIST_IGNORE_DUPS
setopt HIST_IGNORE_ALL_DUPS
setopt HIST_FIND_NO_DUPS
setopt HIST_SAVE_NO_DUPS

# Mimicking fg behavior from bash (fg 1 instead of fg %1)
# https://stackoverflow.com/questions/32614648/weird-jobs-behavior-within-zsh
fg() {
    if [[ $# -eq 1 && $1 = - ]]; then
        builtin fg %-
    else
        builtin fg %"$@"
    fi
}

# Oh my zsh stuff
source $ZSH/oh-my-zsh.sh

# Ctrl+Space to accept suggestion from zsh-autosuggestions (MUST come after sourcing oh my zsh stuff see https://github.com/zsh-users/zsh-autosuggestions/issues/471#issuecomment-573500890)
bindkey '^ ' autosuggest-accept

# Syntax highlighting comes as RPM in Fedora. Must be sourced at the end
if [ -f /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh ]; then
    source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
elif [ -f $HOME/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh ]; then
    source $HOME/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
fi

# Improve paste performances https://github.com/zsh-users/zsh-syntax-highlighting/issues/295#issuecomment-214581607
zstyle ':bracketed-paste-magic' active-widgets '.self-*'

# Do not sort with fzf-tab when doing "git checkout [tab]"
zstyle ':completion:*:git-checkout:*' sort false

# Custom aliases bash/zsh, must be at the end to override some stuff sourced before by zsh
if [ -f ${HOME}/.bash_aliases ]; then
    source ${HOME}/.bash_aliases
fi

# Disable graphical SSH ask pass
[ -n "$SSH_CONNECTION" ] && unset SSH_ASKPASS
export GIT_ASKPASS=

# do not suggest . and .. when doing cd <TAB>
# for some reason I have to put it last
zstyle ':completion:*' special-dirs false
~
```

## File managers

Thunar on the GUI and `nnn` on the terminal.

I use a few plugins in `nnn`. See `NNN_PLUG` above.

## Launcher

Ulauncher. Unchanged.

## Editor

vim and VScode. Unchanged. Even though VScode is becoming some kind of Frankenstein these days.

## Desktop screenshots and recording

Flameshot and Peek. Unchanged.

## Bookmarks

Self hosted shaarli instance. Unchanged.

## Notification

Self hosted Gofify. Unchanged.

## Music player

`cmus`. Unchanged.

## Conclusion

Overall, I'm more efficient around windows management with bspwm.

These are the two essential configuration files to get started with bspwm. Adjust as needed.

`$HOME/.config/bspwm/bspwmrc`:

```bash
#! /bin/sh

pgrep -x sxhkd > /dev/null || sxhkd &

bspc monitor -d 1 2 3 4 5 6 7 8 9 10 11 12

bspc config remove_disabled_monitors true
bspc config remove_unplugged_monitors true
bspc config merge_overlapping_monitors true
bspc config honor_size_hints false
bspc config border_width         2
bspc config window_gap           12
bspc config split_ratio          0.60
bspc config focus_follows_pointer true
bspc config center_pseudo_tiled true
bspc config pointer_action1 move
bspc config pointer_action2 resize_corner
bspc config presel_feedback_color \#5e81ac
bspc config focused_border_color \#5e81ac
bspc config normal_border_color \#2e3440
bspc config directional_focus_tightness low # allows to focus floating windows

### RULES ###
bscp rule -r "*"

bspc rule -a Firefox desktop='^1' --one-shot follow=on
bspc rule -a discord desktop='^2'
bspc rule -a Signal desktop='^2' follow=on
bspc rule -a TelegramDesktop desktop='^2' follow=on
bspc rule -a Terminator desktop='^3' --one-shot follow=on
bspc rule -a Code desktop='^4' --one-shot
bspc rule -a code desktop='^4' --one-shot
bspc rule -a Thunar desktop='^5' follow=on --one-shot
bspc rule -a Zim desktop='^6' --one-shot
bspc rule -a "Rapid Photo Downloader" desktop='^7'
bspc rule -a Rawtherapee desktop='^7'
bspc rule -a Gnucash desktop='^8'
bspc rule -a Bitwarden state=floating rectangle=850x600+100+100
bspc rule -a Lxappearance state=floating
bspc rule -a Pavucontrol state=floating
bspc rule -a Ulauncher state=floating border=off
bspc rule -a Seahorse state=floating
bspc rule -a Sxiv state=floating border=off
bspc rule -a Virt-manager state=floating rectangle=800x600+100+100
bspc rule -a xpad state=floating rectangle=350x350+400+300
bspc rule -a "Seafile Client" desktop='^12' state=floating rectangle=350x350+400+300 border=off

### DEFAULT APPLICATIONS ###
xdg-mime default sxiv.desktop image/jpg
xdg-mime default sxiv.desktop image/jpeg
xdg-mime default sxiv.desktop image/png
xdg-mime default vlc.desktop image/mp4

### STARTUP APPLICATIONS ###
xss-lock xsecurelock &
killall polybar
polybar -q personal -c $HOME/.config/polybar/blocks/config.ini &
pgrep -x dunst > /dev/null || dunst &
pgrep -x xpad > /dev/null || xpad &
feh --bg-fill --no-fehbg ~/Seafile/Wallpaper/000-DEFAULT-WALLPAPER
pgrep -x thunar > /dev/null || thunar --daemon &
nm-applet &
seafile-applet &
pgrep -x xfce4-clipman > /dev/null || xfce4-clipman &
pgrep -x pasystray > /dev/null || pasystray &
pgrep -x ulauncher > /dev/null || ulauncher --hide-window --no-window-shadow &
pgrep -x xfce4-power-manager> /dev/null || xfce4-power-manager &
pgrep -x autokey-gtk > /dev/null || autokey-gtk &
/usb/bin/pulseaudio --system --daemonize --disallow-exit --disallow-module-loading &
```

`$HOME/.config/bspwm/sxhkdrc`:

```bash
super + h
	$HOME/.config/bspwm/hkhelper.sh

# Terminal
super + Return
	terminator

# Reload bspwm
super + Escape
	notify-send -i dialog-information-symbolic.symbolic -u normal -t 3000 "bspwm" "Reloaded"; bspc wm -r

# Restart sxhkd
shift + super + Escape
	notify-send -i dialog-information-symbolic.symbolic -u normal -t 3000 "sxhkd" "Restarted"; pkill -USR1 -x sxhkd

# Restart Polybar
ctrl + super + Escape
	notify-send -i dialog-information-symbolic.symbolic -u normal -t 3000 "Polybar" "restarted"; pkill -USR1 -x polybar; sleep 1; bspc wm -r

# Power menu
super + p
	$HOME/.config/rofi/powermenu.sh

# Close application
shift + super + q
	bspc node -c

# File explorer
super + e
	$HOME/.config/bspwm/thunar.sh

# Favorite applications menu
super + apostrophe
	$HOME/.config/rofi/apps.sh

# Mute notifications
super + i
	dunstctl set-paused toggle

# Clipboard manager
ctrl + alt + h
	/usr/bin/xfce4-popup-clipman

# Lock session
super + l
	xsecurelock

# Tiled mode
super + t
    if [ -z "$(bspc query -N -n focused.tiled)" ]; then \
        bspc node focused -t tiled; \
	    notify-send -u low -t 800 "Tiled"; \
    else \
	    notify-send -u normal -t 800 "Already tiled"; \
    fi

# Toggle between pseudo tiled and tiled
super + y
    if [ -z "$(bspc query -N -n focused.pseudo_tiled)" ]; then \
        bspc node focused -t pseudo_tiled; \
	    notify-send -u low -t 800 "Pseudo tiled"; \
    else \
        bspc node focused -t tiled; \
	    notify-send -u low -t 800 "Undo tiled"; \
    fi

# Toggle between floating and tiled
super + f
    if [ -z "$(bspc query -N -n focused.floating)" ]; then \
        bspc node focused -t floating; \
	    notify-send -u low -t 800 "Floating"; \
    else \
        bspc node focused -t tiled; \
	    notify-send -u low -t 800 "Undo floating"; \
    fi

# Toggle between fullscreen and tiled
super + g
    if [ -z "$(bspc query -N -n focused.fullscreen)" ]; then \
        bspc node focused -t fullscreen; \
	    notify-send -u low -t 800 "Fullscreen"; \
    else \
        bspc node focused -t tiled; \
	    notify-send -u low -t 800 "Undo fullscreen"; \
    fi

# Toggle sticky mode
super + s
    bspc node focused --flag sticky; \
    window_name=$(bspc query -T -n $(bspc query -N -n focused) | jq -r .client.className); \
    if [ -z "$(bspc query -N -n focused.sticky)" ]; then \
        notify-send -u low -t 1500 "Unsticky $window_name"; \
    else \
	    notify-send -u low -t 1500 "Sticky $window_name"; \
    fi

# Rotate the tree rooted at the selected node
super + x
    bspc node -f @parent; bspc node -R 90; bspc node --focus

# Keyboard layout
super + k
	$HOME/.config/rofi/keyboard.sh

# Change monitor
{XF86Display,super + m}
	$HOME/.config/rofi/monitor.sh

# Mute
super + v
    dunstify --urgency=critical --replace 2 -t 3000 --icon=audio-input-microphone-symbolic.symbolic "Toggling microphone" && pactl set-source-mute @DEFAULT_SOURCE@ toggle

# App switcher and scratchpad manager
alt + Tab
        rofi -show window -window-thumbnail

# Swap windows {Left,Right,Up,Down}
super + alt + {Left,Right,Up,Down}
	bspc node --swap {west,east,north,south}

# Focus window {left,right,up,down}
super + {Left,Right,Up,Down}
	bspc node --focus {west,east,north,south}

# Change desktop {1-9,10,11}
super + {1-9,0,minus,equal}
    desktop='^{1-9,10,11,12}'; \
    bspc query -D -d "$desktop.focused" && bspc desktop -f last || bspc desktop -f "$desktop"

# Move windows to desktop {1-9,10,11}
shift + super + {1-9,0,minus}
	bspc node -d '^{1-9,10,11}'

# Move and follow window to desktop {1-9,10,11}
shift + ctrl + super + {1-9,0,minus,equal}
    id=$(bspc query -N -n); bspc node -d ^{1-9,10,11,12}; bspc node -f $id

# Put in scratchpad
super + z
	bspc node focused -t floating; bspc node -d '^12'

# Focus {previous,next} occupied desktop
super + {q,w}
	bspc desktop --focus {prev,next}.occupied

# Circulate tree {backward,forward}
super + bracket{left,right}
	bspc node @/ --circulate {backward,forward}

# Grow gap
shift + super + alt + equal
	gap=$(bspc query --tree --monitor | jq '.windowGap'); new_gap=$(( $gap + 5 )); bspc config window_gap ${new_gap}

# Shrink gap
shift + super + alt + minus
	gap=$(bspc query --tree --monitor | jq '.windowGap'); new_gap=$(( $gap - 5 )); bspc config window_gap ${new_gap}

# Set gap to 0
shift + super + alt + 0
	bspc config window_gap 0

# Ratio {33,50,66} percent
super + {comma,period,slash}
	$HOME/.config/bspwm/ratio.sh {0.33,0.50,0.66}

# Screenshot
Print
	$HOME/.config/bspwm/screenshot.sh

# Screenshot with upload
super + Print
	$HOME/.config/bspwm/screenshot.sh upload

# Toggle xpad
super + n
	xpad --toggle

# Volume {up,down,mute}
XF86Audio{RaiseVolume,LowerVolume,Mute}
	pactl set-sink-volume @DEFAULT_SINK@ {+10%,-10%,toggle}

# Brightness up
XF86MonBrightnessUp
	brightnessctl set +5% -q; dunstify --replace 1 -t 3000 --icon=daytime-sunrise-symbolic.symbolic "Brightness Up" "$(brightnessctl -m | cut -d',' -f4)"

# Brightness down
XF86MonBrightnessDown
	brightnessctl set 5%- -q; dunstify --replace 1 -t 3000  --icon=daytime-sunset-symbolic.symbolic "Brightness Down" "$(brightnessctl -m | cut -d',' -f4)"

# Preselect next window on {left,down,up,right}
shift + super + {Left,Down,Up,Right}
	bspc node -p {west,south,north,east}

# Cancel the preselection
shift + super + space
	bspc node -p cancel

# Preselect the ratio {1-9}0%
ctrl + super + {1-9}
	bspc node -o 0.{1-9}

# Resize the window on {left,down,up,right}
ctrl + super + {Left,Down,Up,Right}
	$HOME/.config/bspwm/resize.sh {left,down,up,right}

```