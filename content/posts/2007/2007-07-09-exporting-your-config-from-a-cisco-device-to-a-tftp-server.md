---
date: 2007-07-09
title: "Exporting your config from a Cisco device to a TFTP server"
---







categories:
- Hardware
- Linux
- Networks


This has been tested under Ubuntu and a Cisco switch model Catalyst 2950 (IOS 12.1)



**1. Set up your TFTP server**


**$ sudo apt-get install xinetd tftpd tftp**


**$ sudo vi /etc/xinetd.d/tftp**

`service tftp
{
protocol = udp
port = 69
socket_type = dgram
wait = yes
user = nobody
server = /usr/sbin/in.tftpd
server_args = /tftpboot
disable = no
}`


**$ sudo mkdir /tftpboot
$ sudo chmod 777 /tftpboot
$ sudo chown nobody /tftpboot**


**$ sudo /etc/init.d/xinetd start**

**2. Dump your config file from the Cisco switch to your TFTP directory**

switch1#**copy system:running-config tftp://192.168.1.1/switch1-confg**
Address or name of remote host [192.168.1.1]? 
Destination filename [switch1-confg]? 
!!
3028 bytes copied in 1.976 secs (1532 bytes/sec)

**3. We make sure the file is on the TFTP server**

user@ubuntu:/tftpboot$ ls -l
total 20
-rw------- 1 nobody nogroup  3028 2007-07-09 17:39 switch1-confg

