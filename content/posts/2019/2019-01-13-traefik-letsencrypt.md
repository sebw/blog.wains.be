---
date: 2019-01-13
title: "Traefik reverse proxy for containers with Lets Encrypt"
---

IMPORTANT: This blog post covers Traefik version 1.

With Traefik, you can easily reverse proxy your containers, and automatically generate a Let's Encrypt certificate for them. It's pretty awesome. No more complicated Nginx containers coupled with another Let's Encrypt companion!

In the following setup, the name of the container will be used for the certificate generation.

For example, if you define `domain = example.org` in Traefik configuration, and your container is called `container01`, a certificate will automatically be generated for `container01.example.org`.

It is advised to create a wildcard DNS record `*.example.org` pointing to the IP address of the server hosting Traefik.

Also, make sure you open port TCP/80 and TCP/443.

The following expects you are already running Docker CE.

Create the file that will contain certificate data:

```
touch /opt/docker/revproxy/acme.json
chmod 600 /opt/docker/revproxy/acme.json
```
Create Traefik configuration `/opt/docker/revproxy/traefik.toml`:

```
#debug = true

logLevel = "ERROR" #DEBUG, INFO, WARN, ERROR, FATAL, PANIC
InsecureSkipVerify = true 
defaultEntryPoints = ["http", "https"]

[docker]
domain = "example.org"
watch = true
exposedbydefault = false

[web]
address = ":18080"
   [web.auth.basic]
   users = ["admin:put_your_encrypted_password_here"]

[entryPoints]
  [entryPoints.http]
  address = ":80"
    [entryPoints.http.redirect]
      entryPoint = "https"
  [entryPoints.https]
  address = ":443"
    [entryPoints.https.tls]

[acme]
email = "john.doe@example.org"
storage = "/etc/traefik/acme.json"
entryPoint = "https"
onHostRule = true
[acme.httpChallenge]
entryPoint = "http"
```

Now start Traefik container:

```
docker run -d --name revproxy -p 80:80 -p 443:443 --restart=always -l traefik.enable=true -l traefik.frontend.rule=Host:revproxy.example.org -l traefik.port=18080 -v /opt/docker/revproxy/acme.json:/etc/traefik/acme.json -v /opt/docker/revproxy/traefik.toml:/etc/traefik/traefik.toml -v /var/run/docker.sock:/var/run/docker.sock traefik
```

Then start any container (for example called `blah`), and make sure they have a label `traefik.enable=true`.

It should take a couple of seconds only for the certificate to get generated. The timestamp of `acme.json` is a good indicator. You should now be able to surf on `https://blah.example.org` with a valid certificate.

If your container exposes a port other than `80`, then you should pass the label `traefik.port`.

Example: `docker run -d --name whoami -l traefik.port=8000 -t jwilder/whoami`

For containers exposing their service on TLS, pass the label `traefik.protocol=https`.