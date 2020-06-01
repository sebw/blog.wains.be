---
date: 2013-07-17
title: "Manipulate image metadata (EXIF, IPTC) with open source tools"
---
Date: 2013-07-17
tags: Linux

Rename picture based on capture date:

	find -name 'IMG*.JPG' | while read PIC; do DATE=$(exiftool -p '$DateTimeOriginal' $PIC | sed 's/[: ]//g'); touch - t $(echo $DATE | sed 's/\(..$\)/\.\1/') $PIC;echo "Date:" $DATE "- " $PIC; mv -i $PIC $(dirname $PIC)/`date +%Y%m%d_%T`_$DATE.jpg; done

Rename: 

	exit2 -t *.jpg
	exiv2 -r Description_%Y%m%d_%H%M%S *.jpg

Add copyright:

	exiftool -copyright="John Doe" *.jpg

Add EXIF comment:

	exiv2 -M"set Exif.Photo.UserComment charset=Ascii This is a comment" *.JPG
