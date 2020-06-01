---
date: 2006-08-15
title: "Nautilus shortcut for root under users session"
---







categories:
- Linux


As root, create a file called "Nautilus-root.desktop" into /usr/share/applications
 
`[Desktop Entry]
Name=File Browser (Root)
Comment=Browse the filesystem with the file manager
TryExec=nautilus
Exec=sudo nautilus --no-desktop --browser %U
Icon=file-manager
Terminal=false
StartupNotify=true
Type=Application
Categories=GNOME;Application;System;Utility;Core;X-Red-Hat-Base;
OnlyShowIn=GNOME;
X-GNOME-Bugzilla-Bugzilla=GNOME
X-GNOME-Bugzilla-Product=nautilus
X-GNOME-Bugzilla-Component=general
X-Desktop-File-Install-Version=0.10`

When done, killall gnome-panel

After gnome is done restarting, you'll notice a new shortcut under Applications > System tools

Users need to be in the sudoers to be able to execute the file browser... 
Howto : [http://blog.wains.be/?p=112](http://blog.wains.be/?p=112)


