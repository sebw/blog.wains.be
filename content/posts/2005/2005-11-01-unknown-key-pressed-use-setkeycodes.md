---
date: 2005-11-01
title: "Unknown key pressed, use setkeycodes"
---

Under Ubuntu Breezy, I kept getting an error message fulfilling **/var/log/messages** whenever I was pressing any arrow keys :

`Oct 30 10:50:27 pc1 kernel: [4323215.043000] atkbd.c: Unknown key pressed (translated set 2, code 0xaa on isa0060/serio0).
Oct 30 10:50:27 pc1 kernel: [4323215.043000] atkbd.c: Use 'setkeycodes e02a keycode' to make it known.`

Adding this line to **/etc/sysctl.conf** fixed the problem :
`setkeycodes e02a 104`

(104 is for page up key as far as I know)
