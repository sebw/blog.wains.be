---
date: 2009-09-13
title: "WPA2 + FreeRADIUS + EAP-TLS"
---







categories:
- Debian/Ubuntu
- Howto
- Networks
- RADIUS
- Security
- Wifi


_WPA/WPA2 Enterprise on your wired or wireless network. Tested under Debian Lenny (server side) and Mac OS X 10.6, Windows XP and Android 2.2, Ubuntu 10.10 (client side)_

**BUILDING AND INSTALLING FREERADIUS WITH TLS SUPPORT**

This step is not needed anymore, starting from Debian Squeeze Freeradius comes with TLS support.
Please, do not build stuff on your production server. Build on a dedicated build machine and then install the resulting packages on the production server.

If you are running Debian Lenny on i386, you can skip the build steps and grab the packages at this address :
[http://blog.wains.be/pub/freeradius-tls/](http://blog.wains.be/pub/freeradius-tls/)

Let's proceed...

**Install the necessary packages :**

`apt-get install dpkg-dev fakeroot`

**Download the source :**

`cd /root
mkdir freeradius-tls
cd freeradius-tls
apt-get source freeradius`

**Make the changes :**

Edit /root/freeradius-tls/debian/rules :

and change 
`--with`
by 
`--without `
for eap_tls, eap_ttls, eap_peap and openssl

Just as :
`--with-rlm_eap_tls 
--with-rlm_eap_ttls 
--with-rlm_eap_peap 
--without-rlm_eap_tnc 
--without-rlm_otp 
--with-rlm_sql_postgresql_lib_dir=`pg_config --libdir`
--with-rlm_sql_postgresql_include_dir=`pg_config --includedir` 
--with-openssl 
--without-rlm_eap_ikev2 
--without-rlm_sql_oracle 
--without-rlm_sql_unixodbc `

Then, comment the following :
`for pkg in ${pkgs} ; do 
if dh_shlibdeps -p $$pkg -- -O 2>/dev/null | grep -q libssl; then 
echo "$$pkg links to openssl" ;
exit 1 ;
fi ;
done`

Edit /root/freeradius-tls/debian/control :
On the line beginning by "Build-Depends" 
Add the folowing :
`", libssl-dev" `
at the end of the line (without the quotes)

Install dev libraries :
`apt-get install libssl-dev debhelper libgdbm-dev libiodbc2-dev libkrb5-dev libldap2-dev libltdl3-dev libmysqlclient15-dev libpam0g-dev libpcap-dev libperl-dev libpq-dev libsasl2-dev libsnmp-dev python-dev`

Build freeradius :
`dpkg-buildpackage -rfakeroot`

Building will end by a warning message, this is not important.

Put the packages on hold to avoid upgrading with a non-TLS version of FreeRADIUS  :

`echo "freeradius hold" | dpkg --set-selections
echo "libfreeradius2 hold" | dpkg --set-selections
echo "freeradius-common hold" | dpkg --set-selections`

Install the packages we've just built :
`dpkg --install freeradius_2.0.4+dfsg-6_i386.deb freeradius-common_2.0.4+dfsg-6_all.deb libfreeradius2_2.0.4+dfsg-6_i386.deb`


**CERTIFICATES **

**Creating the CA**

`apt-get install openssl`


Edit /etc/ssl/openssl.cnf

`[ CA_default ]
dir = ./PKI`


Edit /usr/lib/ssl/misc/CA.sh

`CATOP=./PKI`

Then type :

`cd /etc/ssl
/usr/lib/ssl/misc/CA.sh -newca`

Set a challenge password and a passphrase. This is needed.
The CA created will be copied to the server and clients later on.


**Optional : if you have Windows XP clients**

Create /etc/openssl/PKI/xpextensions


    
    <code>[xpclient_ext]
    extendedKeyUsage=1.3.6.1.5.5.7.3.2
    
    [xpserver_ext]
    extendedKeyUsage=1.3.6.1.5.5.7.3.1</code>





**Server certificate signing request :**

`cd /etc/ssl
openssl req -new -nodes -keyout PKI/server_key.pem -out PKI/server_req.pem -days 730 -config openssl.cnf`

Set a challenge password


**Sign the server certificate request :**
`cd /etc/ssl
openssl ca -config openssl.cnf -policy policy_anything -out PKI/server_cert.pem -infiles PKI/server_req.pem`

Then :
`cp PKI/server_cert.pem PKI/server_cert.pem-backup`

Edit server_cert.pem
Remove everything before the line -----BEGIN CERTIFICATE----- (this is needed for winxp clients)

Next :
`cat PKI/server_key.pem PKI/server_cert.pem > PKI/server_keycert.pem`




**Create a client certificate signing request :**
`openssl req -new -keyout PKI/client_key.pem -out PKI/client_req.pem -days 730 -config openssl.cnf`

You MUST specify a Common Name (CN).

**Sign client cert request :**

Windows xp client :
`openssl ca -config openssl.cnf -policy policy_anything -out PKI/client_cert.pem -extensions xpclient_ext -extfile PKI/xpextensions -infiles PKI/client_req.pem`
Mac OS X / Linux / Android client : 
`openssl ca -config openssl.cnf -policy policy_anything -out PKI/client_cert.pem -infiles PKI/client_req.pem`



**Export P12 certs :**

Windows and Mac clients :
`openssl pkcs12 -export -in PKI/client_cert.pem -inkey PKI/client_key.pem -out PKI/client_cert.p12 -clcerts`

Android clients :
`openssl pkcs12 -export -in PKI/client_cert.pem -inkey PKI/client_key.pem -certfile PKI/cacert.pem -name "Wifi" -out PKI/client_cert.p12`

You don't need to export P12 certificates for Linux (smart OS, eh ?)


**FREERADIUS CONFIG**

Do :
`mkdir /etc/freeradius/certs/
cp /etc/ssl/PKI/cacert.pem /etc/freeradius/certs/cacert.pem
cp /etc/ssl/PKI/server_keycert.pem /etc/freeradius/certs/server_keycert.pem`

Then :
`cd /etc/freeradius/certs
openssl dhparam -check -text -5 512 -out dh
dd if=/dev/urandom of=random count=2
chown freerad dh
chmod o-w dh`

Next : 
`cp /etc/freeradius/eap.conf /etc/freeradius/eap.conf-default`

/etc/freeradius/eap.conf :


    
    <code>eap {
            default_eap_type = tls
            timer_expire     = 60
            ignore_unknown_eap_types = no
            cisco_accounting_username_bug = no         
    
            tls {          
                    certdir = ${confdir}/certs
                    cadir = ${confdir}/certs
                    private_key_password = whatever
                    private_key_file = ${certdir}/server_keycert.pem
                    certificate_file = ${certdir}/server_keycert.pem
                    CA_file = ${cadir}/cacert.pem
                    dh_file = ${certdir}/dh
                    random_file = ${certdir}/random
                    fragment_size = 1024
                    include_length = yes
                    check_cert_cn = %{User-Name}
                    cipher_list = "DEFAULT"
            }              
    } </code>





**Edit /etc/freeradius/clients.conf**

We will consider the access-point that will authenticate users against the RADIUS server has the IP 192.168.7.45 :


    
    <code>client localhost {
            ipaddr = 127.0.0.1
            secret          = testing123
            require_message_authenticator = no
            nastype     = other     # localhost isn't usually a NAS...
    }
    
    
    client 192.168.7.45 {
            secret = suchasecurepassword
            shortname = linksys
    }</code>





**Start FreeRADIUS :**

The first time it is recommended to launch with the following command, this gives a lot of output.
`freeradius -X -f`

When everything is fine and clients are happy, start the service the usual way :
`/etc/init.d/freeradius start`


**Set up wifi access point for authentication against our new RADIUS server**

It depends on your hardware here.
You must usually go under the security panel of your device, where you can specify the IP/hostname and port of the RADIUS server, and the password (in our example : suchasecurepassword).

Make sure your firewall lets the Wi-Fi access point talk to FreeRADIUS on port UDP/1812.


**Configure clients**

You'll need the following files for the following platforms :

Mac OS X : P12 certificate (cert + private key), PEM CA certificate 
Windows XP : P12 certificate (cert + private key), PEM CA certificate
Android 2.2 : P12 certificate (cert + private key + CA) (see [http://blog.wains.be/post/importing-certificates-on-android-ca-and-client/](http://blog.wains.be/post/importing-certificates-on-android-ca-and-client/))
Linux Ubuntu 10.10 (Network Manager) : PEM certificate, PEM private key, PEM CA certificate



**This post is a stripped down version of the following howto by my colleague Jérôme : **
[http://hanoteau.blogspot.com/2009/03/howto-setup-eap-tls-wpa-network-with.html](http://hanoteau.blogspot.com/2009/03/howto-setup-eap-tls-wpa-network-with.html)
