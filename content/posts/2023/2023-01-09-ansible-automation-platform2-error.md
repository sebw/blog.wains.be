---
date: 2023-01-09
title: "Fix Error: cannot re-exec process to join the existing user namespace in Ansible Automation Platform 2"
---

I have a lab environment running Ansible Automation Platform 2.3 (AAP) that I use for my customer demos.

At the end of 2022 I stopped the VM for a couple of days.

Today I start my AAP VM as usual. Everything seemed to work except when I try to run a simple job template.

I get an error "cannot re-exec process to join the existing user namespace" in the job output.

I try to sync the project and get the same error. I sync other projects and they all fail.

A quick Google search takes me to a Podman issue [https://github.com/containers/podman/issues/9137](https://github.com/containers/podman/issues/9137).

Podman indeed is the foundation of AAP under the hood, with the concept called Execution Environments (really, those are just containers).

Podman runs as the unprivileged user `awx`.

I could verify the behavior from the command line:

```bash
[root@aap23 etc]# su - awx -c "podman ps -a"
Error: cannot re-exec process to join the existing user namespace
```

The link above mentions a file `pause.pid` that fixes the problem when deleted.

I find a folder `/tmp/podman-run-988/libpod/tmp` but no `pause.pid`.

It turns out the temporary folder is overriden by AAP.

You can find the actual temp folder with this:

```
[root@aap23 etc] su awx -
[awx@aap23 ~]$ podman --log-level debug ps
INFO[0000] podman filtering at log level debug
DEBU[0000] Called ps.PersistentPreRunE(podman --log-level debug ps)
DEBU[0000] Merged system config "/usr/share/containers/containers.conf"
DEBU[0000] Merged system config "/var/lib/awx/.config/containers/containers.conf"
DEBU[0000] Using conmon: "/usr/bin/conmon"
DEBU[0000] Initializing boltdb state at /var/lib/awx/.local/share/containers/storage/libpod/bolt_state.db
DEBU[0000] Overriding run root "/tmp/podman-run-988/containers" with "/tmp/ansible.d1xwha4l/containers" from database
DEBU[0000] Overriding tmp dir "/tmp/podman-run-988/libpod/tmp" with "/tmp/ansible.d1xwha4l/libpod/tmp" from database
DEBU[0000] systemd-logind: Unknown object '/'.
```

Simple delete the file:

```bash
cd /tmp/ansible.d1xwha4l/libpod/tmp
rm pause.pid
```

I can sync my projects again with no restart needed.