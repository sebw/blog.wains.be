---
date: 2006-11-13
title: "Permission denied (publickey,keyboard-interactive) when trying to ssh from"
---






  terminal while using Keychain'

categories:
- Bash
- Linux
- Security
- SSH


**"Permission denied (publickey,keyboard-interactive)"**

I'm not satisfied by SSH clients under Linux (like putty and the likes), the only ssh client I really enjoy is SecureCRT under Windows, it's a great piece of software (okay it works with wine, I have tested it, but I want to stick withthe CLI under Linux).

**SSH agent forwarding ?**



With SecureCRT, I was able to ssh from machines to machines flawlessly...

I could connect directly to machine A or machine B, or connect to machine A and connect to machine B from there (in other words, connect to machine B through machine A), thanks to SSH agent forwarding capabilities provided by SecureCRT.


Under Linux, I'm used to using keychain ([http://www.gentoo.org/proj/en/keychain/](http://www.gentoo.org/proj/en/keychain/))  to store my ssh key into ssh-agent.

I was then able to connect to machine A or machine B directly.
**I wasn't able to connect from machine B through machine A though.**
--> SSH agent forwarding issue

Edit /etc/ssh/ssh_config (which is your ssh client configuration file)

Make sure you have the "ForwardAgent" line enabled :
`Host *
   ForwardAgent yes`

Now SSH agent forwarding will work.
You'll be able to connect to any machine through any machine.
It helps when scp'ing files from machines to machines.

**Security concern**
When using agent forwarding, your agent is available on any machine to which you forward it to. It means it is accessible by root on those systems. You would not forward the agent to machines you don't fully trust ! Indeed, someone may have a local root access on one of the machines and would be able to use your agent to connect to all the machines your ssh key can get access to.

More info : [http://www.securityfocus.com/infocus/1812](http://www.securityfocus.com/infocus/1812)
