---
date: 2008-07-23
title: "📌 SSH local and remote port forwarding"
---

I use SSH local port forwarding on a daily basis but I rarely use remote port forwarding. Today I forgot (again) about the `GatewayPorts` option, so I decided to write a quick reminder about SSH port forwarding.

### Local port forwarding

When to use? When you need to access a service on a remote server that is not exposed.

`home$ ssh user@work.example.org -L 10000:lan-ip-of-remote-server:80`

SSH exposes a port locally that point to the HTTP service on the remote server:

```bash
home$ netstat -tunelp | grep 10000
tcp        0      0 127.0.0.1:10000         0.0.0.0:*               LISTEN      1000       71679       12468/ssh
```

Now we can point our browser to http://localhost:10000 or use curl:

```bash
home$ curl localhost:10000
```

### Local port forward for anyone at home

When to use? If you want other people on your home subnet to be able to reach the non exposed service running on the remote server.

Just add the option `-g`:

`home$ ssh user@work.example.org -L 10000:lan-ip-of-remote-server:80 -g`

We now see the service is available on all interfaces of your home computer, available for anyone to connect to on the local subnet (provided appropriate firewall rules):

```bash
home$ netstat -tunelp | grep 10000
tcp        0      0 0.0.0.0:10000           0.0.0.0:*               LISTEN      1000       72265       12543/ssh
```

Anyone on your local subnet should be able to open http://your-workstation-ip:10000.

### Remote port forwarding

When to use? Opening a service running on your workstation to people on a remote site.

`home$ ssh user@work.example.org -R 10000:your-workstation-ip:22`

We see on the server at work  that a new port tcp/10000 is listening on the loopback interface:

```bash
work.example.org$ netstat -tunelp | grep 10000
tcp        0      0 127.0.0.1:10000              0.0.0.0:*                   LISTEN      0          73719534   3809/1
```

People logged in on the machine work.example.org now should be able to SSH into your home machine by doing:

`work.example.org$ ssh user@localhost -p 10000`


### Remote port forwarding for anyone at work

When to use? If you want everybody on the subnet at work to be able to SSH into your home machine.

There's no `-g` option for remote forward. You need to change the SSH server configuration of work.example.org, add the following to `/etc/ssh/sshd_config`:

`GatewayPorts yes`

Restart SSH.

Connect just as before:

`home$ ssh user@work.example.org -R 10000:192.168.1.10:22`

Now, the service is exposed globally:

```
work.example.org$ netstat -tunelp | grep 10000
tcp        0      0 0.0.0.0:10000                0.0.0.0:*                   LISTEN      0          73721060   4426/1
```

Provided appropriate firewall rules, anyone at work can now connect to your home machine by SSH via the work server, through port tcp/10000:

`$ ssh anyone@work.example.org -p 10000`

### Notes

- You would need to log in as root if you want services to listen on a port < 1024.
- Don't forget to open necessary ports on any firewall either at home or work.
- Unfortunately you can only forward services running on TCP, but [it is possible to forward UDP through SSH using netcat](https://blog.wains.be/2007/2007-02-13-tunneling-udp-requests-through-ssh/)
