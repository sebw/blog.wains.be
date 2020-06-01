---
date: 2013-07-17
title: "My SSH client configuration"
---
Date: 2013-07-17
Tags: ssh

This is my configuration. It fits my environment, some settings might not be right for you.

	Host *
    	ForwardAgent yes # I only connect to safe servers, not shared servers.
    	GSSAPIAuthentication no # Because sometimes I have to deal with Windows DNS servers
    	AddressFamily inet
    	IdentityFile ~/.ssh/id_rsa
    	SendEnv LANG LC_*
    	HashKnownHosts yes # Hash FQDN in known_hosts
    	GSSAPIDelegateCredentials no
    	EscapeChar ~
    	ServerAliveInterval 60
    	ServerAliveCountMax 60
    	#User root
    	StrictHostKeyChecking ask
    
    	# Check SSHFP record
    	#VerifyHostKeyDNS yes
    
    	ControlMaster auto
    	ControlPath ~/.ssh/ssh_socket_%h-%p-%r
    
    	# Since OpenSSH 5.6
    	ControlPersist 5m
