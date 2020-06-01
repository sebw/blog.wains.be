---
date: 2008-07-15
title: "A VPN for remote users with OpenVPN"
---







categories:
- Networks
- VPN


This article quickly explains how to set up a VPN for your remote users based on OpenVPN in around 5 minutes.
If you want detailed informations about OpenVPN, certificates or other stuff, this is not the right place.

This applies to Debian Etch but will work will little difference in paths under Red Hat.

**On the server**

Install OpenVPN and the dependencies :
`# apt-get install openvpn`

Copy the scripts that will help us generate all the necessary files (certificates, keys, etc.) :

    
    <code>Under Debian :
    # cp -aR /usr/share/doc/openvpn/examples/easy-rsa/2.0 /etc/openvpn/pki
    
    Under Red Hat :
    # cp -aR /usr/share/doc/openvpn-2.0.9/easy-rsa/2.0 /etc/openvpn/pki</code>



Let's start :
`# cd /etc/openvpn/pki`

Edit the variables :
`# vim vars`

export KEY_COUNTRY=US
export KEY_PROVINCE=SC
export KEY_CITY=GREENVILLE
export KEY_ORG="Green Company, LLC"
export KEY_EMAIL="admin@example.org"

Set the scripts executable :
`# chmod +x *`

Source the variables :
`# . ./vars`

Set up the keys environment :
`# ./clean-all`

Build the certificate authority (CA) :
`# ./build-ca`

Build the certificate and private key for the server :
`# ./build-key-server server`

It's not necessary to set a password.

Then, build the Diffie Hellman file :
`# ./build-dh`

Build the client certificate for your first user :
`# ./build-key-pass client1`

You'll be asked questions about the client.


Now under /etc/openvpn/pki/keys you must find a bunch of files.

Copy the files necessary for the server on the server under /etc/openvpn :
ca.crt
server.crt
server.key
dh1024.pem

Do so with a single command :
`# cp /etc/openvpn/pki/keys/{server.crt,server.key,dh1024.pem,ca.crt} /etc/openvpn`


Send the following files to the client, store them wherever pleases you :
ca.crt
client1.crt
client1.key


Send the following configuration to the user "client1" (vpn.conf which would reside in the same directory as the certificates and key) :


    
    <code>remote vpn.example.org
    port 10000
    proto udp
    dev tun
    
    comp-lzo
    
    client
    
    resolv-retry infinite
    nobind
    persist-key
    persist-tun
    ns-cert-type server
    
    verb 3
    
    ca      ./ca.crt
    cert    ./client1.crt
    key     ./client1.key
    
    route 	172.30.0.0 255.255.0.0
    
    #up 	./up.sh
    #down 	./down.sh
    
    ping 10
    ping-restart 60</code>



The up and down scripts are respectively executed when the connection is opened and closed.
You can for example change the name server the client will use when connected to the VPN using those scripts (or opening ports in the firewall, etc.).


**Server (/etc/openvpn/vpn.conf on the server) :**

    
    <code>port    10000
    proto   udp
    dev-type     tun
    dev vpn-user
    
    ca      ca.crt
    cert    server.crt
    key     server.key
    dh      dh1024.pem
    
    # enable compression
    comp-lzo
    
    # allow several users to connect with the same certificate
    duplicate-cn
    
    server  10.30.0.0 255.255.255.0
    client-to-client
    
    keepalive 10 120
    
    push 	 	"dhcp-option DOMAIN local.example.org"
    push 	 	"dhcp-option DNS 172.30.0.254"
    
    persist-key
    persist-tun
    
    user nobody
    group nogroup
    
    log vpn.log
    verb 1</code>



The server will "push" the DNS settings to the client, this will modify your /etc/
resolv.conf so you will use the DNS server and hostname resolution of your office (or whatever place you are connecting to) when connected to the VPN.


On the server, make sure openvpn starts at boot by uncommenting the following line under /etc/default/openvpn (Debian) :
`AUTOSTART="all"`


When done, restart OpenVPN on the server :
`# /etc/init.d/openvpn restart`


Now, connect from the client (as root, or use sudo) from a remote network :
`# openvpn --config vpn.conf`


The client will get an IP in the range 10.30.0.x on the device tun0, it will add a route to the range 172.30.0.0/16 through the VPN (172.30.x.x in this example is supposed to be the network subnet on the server side).
