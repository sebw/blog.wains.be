---
date: 2006-10-16
title: "BackTrack on USB drive"
---







categories:
- Howto
- Linux
- Security


First of, I'd like to thank **[Dries](http://www.dalby.be)** who kept me updated on this issue.

Indeed, back in the BackTrack beta days, you were able to install BT on your USB drive using a tool called MySLAXCreator (http://myslax.bonsonno.org).

When BT Final was released, it was no longer possible to use MySLAXCreator to install it on the USB drive.

The whole process is described by Dries in the comments, but I'm gonna try to make it clearer here.

**1. Download BackTrack 2 Final ISO**

[http://www.remote-exploit.org/backtrack_download.html](http://www.remote-exploit.org/backtrack_download.html)

**2. Copy the content of the ISO to your USB drive**

See here for more info : [link](http://backtrack.offensive-security.com/index.php?title=Howto:USB_Stick)

If you're under Windows, you can use WinRAR.
Under Linux (tested under Ubuntu), a WinRAR like tool will allow you to extract the content to your USB drive.

Extract into the root of the USB drive !

You should now have two new folders at the root of the USB drive (/boot and /bt).

**3. Make the USB stick bootable**

This was the toughest point, the backtrack team made it simple by writing two scripts, one for Windows, and another one for Linux/OS X

Windows :
Under the command line, go under your USB drive, cd into boot, then execute bootinst.bat

I haven't tested this, since I don't have any Windows computer. Feedbacks welcomed as always.

Under Linux/OS X :
`$ cd /media/USB_DRIVE/boot/
$ ./bootinst.sh`

**4. Boot under BackTrack !**

**5. Build a third party software for BackTrack (here aircrack-ptw)**

The usual stuff :
`$ cd /tmp
$ wget http://www.cdc.informatik.tu-darmstadt.de/aircrack-ptw/download/aircrack-ptw-1.0.0.tar.gz
$ tar -xzvf aircrack-ptw-1.0.0.tar.gz
$ cd aircrack-ptw-1.0.0`

Patch the source file and build the binary :
`$ sed -e "s/-O3 -lpcap/-O3/" -e "s/(gcc.*b.c)/1 -lpcap/" Makefile > Makefile.new
$ cp Makefile Makefile.old; mv Makefile.new Makefile
$ make`

Now you should delete everything under /tmp/aircrack-ptw-1.0.0/ except the binary you've just built.

Now always under /tmp/aircrack-ptw-1.0.0 :
`# mkdir -p ./usr/bin

This will create the tree for the module..

Finally create the module and copy it in the backtrack "filesystem" :
`# dir2lzm /tmp/aircrack-ptw-1.0.0/  aircrack-ptw.lzm
