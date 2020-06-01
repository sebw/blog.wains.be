---
date: 2010-04-29
title: "SSH - add port forwardings on a live connection with EscapeChar"
---







categories:
- SSH


Excerpt from the man page :

The interesting part is in bold.


    
    <code>When a pseudo-terminal has been requested, ssh supports a number of functions through
    the use of an escape character.
    
    A single tilde character can be sent as ~~ or by following the tilde by a character
    other than those described below.  The escape character must always follow a newline
    to be interpreted as special.  The escape character can be changed in configuration
    files using the EscapeChar configuration directive or on the command line by the -e
    option.
    
    The supported escapes (assuming the default ‘~’) are:
    
    ~.      Disconnect.
    
    ~^Z     Background ssh.
    
    ~#      List forwarded connections.
    
    ~&      Background ssh at logout when waiting for forwarded connection / X11 sessions
            to terminate.
    
    ~?      Display a list of escape characters.
    
    ~B      Send a BREAK to the remote system (only useful for SSH protocol version 2 and
            if the peer supports it).
    
    <strong>~C      Open command line.  Currently this allows the addition of port forwardings
            using the -L and -R options (see above).  It also allows the cancellation of
            existing remote port-forwardings using -KR[bind_address:]port.  !command
            allows the user to execute a local command if the PermitLocalCommand option is
            enabled in ssh_config(5).  Basic help is available, using the -h option.</strong>
    
    ~R      Request rekeying of the connection (only useful for SSH protocol version 2 and
            if the peer supports it).
    </code>



EscapeChar also allows disconnection of an SSH session, for example when it is hanging and is not giving you the prompt back (~.).
