---
date: 2011-10-25
title: "Two step authentication on SSH with Google Authenticator under Debian Sid"
---







categories:
- Debian/Ubuntu
- Howto
- Security


On a Debian Sid system, install the following :

`apt-get install libpam-google-authenticator`

Edit /etc/ssh/sshd_config and set :

`ChallengeResponseAuthentication yes`
 
Restart the service :

`service ssh restart`

Now run :

`google-authenticator`

Scan the barcode from the Google Authenticator app on your mobile device.

Edit /etc/pam.d/sshd and add at the very beginning of the file :

`auth required pam_google_authenticator.so`

Now test a SSH connection. You should be prompted by a cool "Verification code :"
Then by the regular password prompt.
