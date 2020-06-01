---
date: 2005-10-27
title: "Install CentOS from the web"
---

This could work under Fedora and other RHEL flavored distributions...

- Download the first CD of CentOS 


    
    <code>Edit March 31 2006 :
    
    I found out you can avoid downloading the full first CD
    (See ftp://ftp.belnet.be/mirror/ftp.centos.org/4/os/i386/images/README)
    
    This directory contains image files that can be used to create media
    capable of starting the CentOS-4 i386 installation process.
    
    The boot.iso file is an ISO 9660 image of a bootable CD-ROM.  It is useful
    in cases where the CD-ROM installation method is not desired, but the
    CD-ROM's boot speed would be an advantage.
    
    To use this image file, burn the file onto CD-R (or CD-RW) media as you
    normally would.
    
    The diskboot.img file is a VFAT filesystem image that can be written to a
    USB pendrive or other bootable media larger than a floppy.  Note that
    booting via USB is dependent on your BIOS supporting this.  It should
    be written to the device using dd.
    
    Download boot.iso : ftp://ftp.belnet.be/mirror/ftp.centos.org/4/os/i386/images/boot.iso
    Download diskboot.img : ftp://ftp.belnet.be/mirror/ftp.centos.org/4/os/i386/images/diskboot.img</code>



- Boot your machine using the CD
- When asked, type "linux askmethod"
- You'll be asked for the method to use : local, NFS, HTTP, FTP... pick up HTTP
- After a few steps (language and input preferences), you'll need to setup your IP configuration, either by using DHCP or manually
- You'll be asked the server and folder containing the installation files of CentOS :
--> Server : ftp.belnet.be
--> Folder : /packages/centos/4.2/os/i386
- The installer will ask some more questions and will start installing the OS downloading needed packages from the web !

Depending on your connection and options, it could take from around 30 minutes to several hours...
It takes about 45 minutes to complete a workstation configuration (around 2.5 Gb installation uncompressed) using a DSL 3.3 Mbps line
