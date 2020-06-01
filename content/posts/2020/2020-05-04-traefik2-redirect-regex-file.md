---
date: 2020-05-04
title: "Make a regexp redirect in dynamic configuration file with Traefik v2"
---

`/etc/traefik/traefik.yml`:

```yaml
[...]
providers:
  file:
    directory: "/etc/traefik/dynamic/"
    watch: true
[...]
```

`/etc/traefik/dynamic/redirect.yml`:

```yaml
http:
  middlewares:
    redir:
      redirectRegex:
        permanent: true
        regex: "http://old.wains.be/(.*)"
        replacement: "https://new.wains.be/${1}"

  routers:
    redir:
      rule: "HostRegexp(`old.wains.be`)"
      entrypoints:
      - http
      middlewares: 
      - redir
      tls:
        certresolver: "letsencrypt"
      service: "ThisWillNeverBeUsedButNeedsToBeThere"

  services:
    ThisWillNeverBeUsedButNeedsToBeThere:
      loadBalancer:
        servers:
          - url: "http://127.0.0.1"
```

As you can notice, we have to declare a dummy service, otherwise the redirect will never work.

NOTE: if your old address used to be as https, Traefik should have a valid certificate for the URL.

## Docker labels

If you want to make the same with labels on Docker containers, you have to apply labels to a dedicated dummy container.

Here's an Ansible playbook I've used to create the dummy container using `whoami` image.

```yaml
---
- name: redirect
  hosts: vps
  become: true
  tasks:
  - name: whoami
    docker_container:
      name: whoami
      image: 'containous/whoami'
      networks:
        - name: UserDefinedBridge
      purge_networks: yes
      labels:
        traefik.enable: "true"
        ansible: "true"
        traefik.http.routers.prsite.rule: "HostRegexp(`old.wains.be`)"
        traefik.http.routers.prsite.middlewares: "pr-redirect"
        traefik.http.routers.prsite.entrypoints: "https"
        traefik.http.routers.prsite.tls: "true"
        traefik.http.middlewares.pr-redirect.redirectregex.regex: "^https://old.wains.be/(.*)"
        traefik.http.middlewares.pr-redirect.redirectregex.replacement: "https://new.wains.be/$1"
        traefik.http.middlewares.pr-redirect.redirectregex.permanent: "true"
```

The service part should not be defined since the labels are applied to the container which is the service.
