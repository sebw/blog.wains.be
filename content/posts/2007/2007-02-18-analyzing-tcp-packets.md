---
date: 2007-02-18
title: "Analyzing TCP packets"
---







categories:
- RHCE


Reviewing the basics of TCP/IP networking.. I wasn't able to find the command displaying a full detail of datagrams using tcpdump.. And as I tend to always forget how to get that verbose output, I'll post it here for good.

Had to install Wireshark, as I couldn't find how to get it under tcpdump (drop a comment if you know the answer ! :) )

Command :
`$ tshark -V -i eth0`

Output :

`Frame 1 (91 bytes on wire, 91 bytes captured)
    Arrival Time: Feb 18, 2007 17:53:58.431551000
    [Time delta from previous packet: 0.000000000 seconds]
    [Time since reference or first frame: 0.000000000 seconds]
    Frame Number: 1
    Packet Length: 91 bytes
    Capture Length: 91 bytes
    [Frame is marked: False]
    [Protocols in frame: eth:ip:tcp:ssl]
Ethernet II, Src: 3Com_1e:xx (00:04:76:xx), Dst: BillionE_00:xx (00:04:ed:xx)
    Destination: BillionE_00:xx (00:04:ed:xx)
        Address: BillionE_00:xx (00:04:ed:xx)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
    Source: 3Com_1e:xx (00:04:76:xx)
        Address: 3Com_1e:xx (00:04:76:xx)
        .... ...0 .... .... .... .... = IG bit: Individual address (unicast)
        .... ..0. .... .... .... .... = LG bit: Globally unique address (factory default)
    Type: IP (0x0800)
Internet Protocol, Src: 192.xx (192.xx), Dst: 66.xx (66.xx)
    Version: 4
    Header length: 20 bytes
    Differentiated Services Field: 0x00 (DSCP 0x00: Default; ECN: 0x00)
        0000 00.. = Differentiated Services Codepoint: Default (0x00)
        .... ..0. = ECN-Capable Transport (ECT): 0
        .... ...0 = ECN-CE: 0
    Total Length: 77
    Identification: 0x719e (29086)
    Flags: 0x04 (Don't Fragment)
        0... = Reserved bit: Not set
        .1.. = Don't fragment: Set
        ..0. = More fragments: Not set
    Fragment offset: 0
    Time to live: 63
    Protocol: TCP (0x06)
    Header checksum: 0x6850 [correct]
        [Good: True]
        [Bad : False]
    Source: 192.xx (192.xx)
    Destination: 66.xx (66.xx)
Transmission Control Protocol, Src Port: 39076 (39076), Dst Port: https (443), Seq: 0, Ack: 0, Len: 37
    Source port: 39076 (39076)
    Destination port: https (443)
    Sequence number: 0    (relative sequence number)
    [Next sequence number: 37    (relative sequence number)]
    Acknowledgement number: 0    (relative ack number)
    Header length: 20 bytes
    Flags: 0x18 (PSH, ACK)
        0... .... = Congestion Window Reduced (CWR): Not set
        .0.. .... = ECN-Echo: Not set
        ..0. .... = Urgent: Not set
        ...1 .... = Acknowledgment: Set
        .... 1... = Push: Set
        .... .0.. = Reset: Not set
        .... ..0. = Syn: Not set
        .... ...0 = Fin: Not set
    Window size: 6610
    Checksum: 0x9d2b [correct]
Secure Socket Layer
    TLSv1 Record Layer: Encrypted Alert
        Content Type: Alert (21)
        Version: TLS 1.0 (0x0301)
        Length: 32
        Alert Message: Encrypted Alert`

