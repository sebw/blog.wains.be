---
date: 2010-09-02
title: "Setting up Synergy between Mac OS and Linux"
---







categories:
- Apple/Mac OS
- Linux


_This post should be OK for any kind of client/server combination. That's just that I only had a MacBook and a Linux netbook under my hands at the moment of writing this._

Quoting their homepage : "Synergy lets you easily share a single mouse and keyboard between multiple computers with different operating systems, without special hardware. All you need is a LAN connection. It's intended for users with multiple computers, where each system uses its own display."

They forget to mention clipboard along with mouse and keyboard sharing. But it's not completely working for me. See below.

For the setup, either see here : [http://synergy-foss.org/pm/projects/synergy/wiki/Setup](http://synergy-foss.org/pm/projects/synergy/wiki/Setup)

Or for the quick way :

**On the Mac (Synergy server, right screen) :**

Edit synergy.conf somewhere on the filesystem :


    
    <code>section: screens
       mac.intranet.example.com:
       linux.intranet.example.com:
    end
    section: links
       mac.intranet.example.com:
           left = linux.intranet.example.com
       linux.intranet.example.com:
           right = mac.intranet.example.com
    end</code>



Start the server :
`synergys  -f --config synergy.conf`

**Under Linux (Synergy client, left screen) :**

`synergyc -f mac.intranet.example.com`

Done !

As said earlier, I was able to capture the clipboard, but only from Linux to Mac. Indeed, for some reason it didn't work the other way around.

Don't forget : if you want to get rid of any keyboard or mouse on the client computer, make sure synergy starts at boot ;-)

**And finally a word of caution from Synergy FAQ, as Synergy is potentially sensitive to MITM attacks and eavesdropping :**

_What security/encryption does synergy provide?
Synergy provides no built-in encryption or authentication. Given that, synergy should not be used on or over any untrusted network, especially the Internet. It's generally fine for home networks. Future versions may provide built-in encryption and authentication.

Strong encryption and authentication is available through SSH (secure shell). Run the SSH daemon (i.e. server) on the same computer that you run the synergy server. It requires no special configuration to support synergy. On each synergy client system, run SSH with port forwarding:

        ssh -f -N -L 24800:server-hostname:24800 server-hostname
where server-hostname is the name of the SSH/synergy server. Once ssh authenticates itself, start the synergy client normally except use localhost or 127.0.0.1 as the server's address. SSH will then encrypt all communication on behalf of synergy. Authentication is handled by the SSH authentication._


