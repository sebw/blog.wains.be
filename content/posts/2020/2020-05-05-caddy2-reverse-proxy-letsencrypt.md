---
date: 2020-05-05
title: "Minimal Caddy2 reverse proxy + letsencrypt for your Docker containers"
---

For the demonstration purpose, let's create a container, we can use `whoami`:

```bash
docker run --name whoami --net userbridge containous/whoami
```

Create the configuration file for Caddy, for example under `/opt/docker/caddy/Caddyfile`:

```json
{
    # email to use on Let's Encrypt
    email youremail@example.org
    
    #acme_ca https://acme-staging-v02.api.letsencrypt.org/directory
    #debug
}

example.org {
	file_server
}

who.example.org {
    reverse_proxy http://whoami:80
}
```

Create your Caddy container:

```bash
docker run -d -p 80:80 -p 443:443 \
    --name caddy \
    --net userbridge \
    -v /opt/docker/caddy/Caddyfile:/etc/caddy/Caddyfile \
    -v /opt/docker/caddy/data:/data \
    caddy
```

Caddy should sit in the same network as the container (here `userbridge`) you want to reverse proxy.

The logs should show the certificate generation progress:

```bash
$ sudo docker logs -f caddy
[...]
{"level":"info","ts":1588691563.4889822,"logger":"http","msg":"enabling automatic TLS certificate management","domains":["who.example.org","example.org"]}
2020/05/05 15:12:43 [INFO][who.example.org] Obtain certificate; acquiring lock...
2020/05/05 15:12:43 [INFO][who.example.org] Obtain: Lock acquired; proceeding...
{"level":"info","ts":1588691563.5040448,"msg":"autosaved config","file":"/config/caddy/autosave.json"}
{"level":"info","ts":1588691563.5040858,"msg":"serving initial configuration"}
2020/05/05 15:12:44 [INFO][who.example.org] Waiting on rate limiter...
2020/05/05 15:12:44 [INFO][who.example.org] Done waiting
2020/05/05 15:12:44 [INFO] [who.example.org] acme: Obtaining bundled SAN certificate given a CSR
2020/05/05 15:12:44 [INFO] [who.example.org] AuthURL: https://acme-v02.api.letsencrypt.org/acme/authz-v3/XXXXXXXXX
2020/05/05 15:12:44 [INFO] [who.example.org] acme: Could not find solver for: tls-alpn-01
2020/05/05 15:12:44 [INFO] [who.example.org] acme: use http-01 solver
2020/05/05 15:12:44 [INFO] [who.example.org] acme: Trying to solve HTTP-01
2020/05/05 15:12:45 [INFO][who.example.org] Served key authentication (HTTP challenge)
2020/05/05 15:12:45 [INFO][who.example.org] Served key authentication (HTTP challenge)
2020/05/05 15:12:48 [INFO][who.example.org] Served key authentication (HTTP challenge)
2020/05/05 15:12:55 [INFO][who.example.org] Served key authentication (HTTP challenge)
2020/05/05 15:13:01 [INFO] [who.example.org] The server validated our request
2020/05/05 15:13:01 [INFO] [who.example.org] acme: Validations succeeded; requesting certificates
2020/05/05 15:13:02 [INFO] [who.example.org] Server responded with a certificate.
2020/05/05 15:13:02 [INFO][who.example.org] Certificate obtained successfully
2020/05/05 15:13:02 [INFO][who.example.org] Obtain: Releasing lock
```

