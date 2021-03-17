---
date: 2021-03-17
title: "FRP - an open source alternative to ngrok"
---

If you want to expose a service from your LAN to the internet and you can't open ports, you can use ngrok, or its open source alternative FRP.

## Server

The server will be the middle man between your service and the person visiting.

Install FRP server (called frps).

I installed my instance on Docker using Ansible and the `cloverzrg/frps-docker` image:

```
---
- name: frps tunnel server side
  hosts: vps
  gather_facts: false
  become: true
  tasks:

  - name: frps
    docker_container:
      name: tunnel
      image: cloverzrg/frps-docker
      networks:
        - name: UserDefinedBridge
      purge_networks: yes
      restart_policy: "always"
      volumes:
      - /opt/docker/tunnel.example.org/conf:/conf
      ports:
      - "7700:7700"
      - "28443:28443"
      - "28080:28080"
      labels:
        traefik.enable: "true"
        ansible: "true"
        traefik.http.routers.tunneladmin.entrypoints: "https"
        traefik.http.routers.tunneladmin.service: "tunneladmin"
        traefik.http.routers.tunneladmin.rule: "Host(`tunnel.example.org`)"
        traefik.http.routers.tunneladmin.tls: "true"
        traefik.http.routers.tunneladmin.tls.certresolver: "letsencrypt"
        traefik.http.services.tunneladmin.loadbalancer.server.port: "7500"
```

FRP comes with a web interface running on port 7500 which I secure behind Traefik.

The server will listen for connections on TCP/7700. Services will be exposed either on port 28080 (http) or 28443 (https).

`/opt/docker/tunnel.example.org/conf/frps.ini`

```
[common]
bind_port = 7700
token = xxx

vhost_http_port = 28080
vhost_https_port = 28443

dashboard_port = 7500
dashboard_user = ricky
dashboard_pwd = zzz

tcp_mux = true
max_pool_count = 10
```

## Client

Let's assume your client runs an HTTP server locally on port 443.

Download the client https://github.com/fatedier/frp/releases/download/v0.36.0/frp_0.36.0_linux_amd64.tar.gz

Unzip.

Create `frpc.ini`

```
[common]
server_addr = server.example.org
server_port = 7700
token = xxx
tcp_mux = true
tls_enable = true

[web]
type = https # this is the service that will define which port will be used publicly (see server config)
local_port = 443 # this is the port of the service you want to expose
custom_domains = your-custom-domain.example.org
```

Run `frpc -c frpc.ini`

Your service should be available at https://your-custom-domain.example.org:28443

For any custom domain to work, you need a wildcard DNS for *.example.org pointing to your server.

Otherwise you need to create the appropriate A records you want to use.