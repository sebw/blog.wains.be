---
date: 2010-08-31
title: "Force SSH password authentication"
---







categories:
- SSH


If for some reason you want to disable public key authentication temporarily when SSH'ing into a machine, type :

`ssh -o PubkeyAuthentication=no user@machine`

You should get the prompt for the password.

This goes without saying, but ChallengeResponseAuthentication (at least on Debian) must be set to yes on the server side for this to work.

Thanks to Philip for proof-reading this post :-)

