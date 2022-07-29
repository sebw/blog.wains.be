---
date: 2022-07-30
title: "Variable substitution with envsubst"
---

If you need to generate configuration files quickly based on templates, you can use envsubst to help you.

On Fedora, envsubst is part of the gettext package, which is installed by default.

Let's imagine this template:

```yaml
  - name: $name
    group: core
    url: "https://$name.wains.be/"
    interval: 5m
    conditions:
      - "[STATUS] == 200"
      - "[CERTIFICATE_EXPIRATION] > 48h"
```

Now export a variable:

```bash
export name=blog
```

Now run envsubst:

```bash
$ envsubst < config.yaml
  - name: blog
    group: core
    url: "https://blog.wains.be/"
    interval: 5m
    conditions:
      - "[STATUS] == 200"
      - "[CERTIFICATE_EXPIRATION] > 48h"
```

You can of course redirect the output to a file:

```bash
$ envsubst < config.yaml > blog.yaml
$ cat blog.yaml
  - name: blog
    group: core
    url: "https://blog.wains.be/"
    interval: 5m
    conditions:
      - "[STATUS] == 200"
      - "[CERTIFICATE_EXPIRATION] > 48h"
```

