---
date: 2022-04-02
title: "Install StackRox on k3s"
---

Tested on k3s 1.22.5.

StackRox has been open sourced a couple of days ago! StackRox has been acquired in February 2021 by Red Hat (my employer) and we stick to our promise to Open Source communities so we finally contributed the code back to the community.

The project is [documented](https://github.com/stackrox/stackrox#deployment) to install on k8s or OpenShift.

I wanted to give the StackRox project a try on my own VPS (running this very blog) but I run k3s on Ubuntu 21.04.

I managed to install without too much troubles, here are the steps:

SSH to your VPS, as root. All the steps below are performed as root.

First of all make sure to install some packages:

```bash
apt-get install wget unzip
```

Download roxctl:

```bash
cd /usr/local/bin
wget https://mirror.openshift.com/pub/rhacs/assets/3.69.0/bin/Linux/roxctl
chmod +x roxctl
```

Check the tags available at https://quay.io/repository/stackrox-io/main?tab=tags

Clone the project:

```bash
git clone https://github.com/stackrox/stackrox
cd stackrox
```

Edit `deploy/common/k8sbased.sh` and at line 101 change to `use_docker=0`

Start the installer with the tag you want to deploy

```bash
MAIN_IMAGE_TAG=3.69.x-nightly-20220402 ./deploy/k8s/deploy-local.sh
```

After a couple of minutes, it should tell you that everything is installed.

You can create your ingress now:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: stackrox-ingress
  namespace: stackrox
  annotations:
	kubernetes.io/ingress.class: traefik
	cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  tls:
	- secretName: stackrox-tls
	  hosts:
		- stackrox.example.org
  rules:
  - host: stackrox.example.org
	http:
	  paths:
		- path: /
		  pathType: Prefix
		  backend:
			service:
			  name: central
			  port:
	        number: 443
```

Apply:

```
kubectl apply -f ingress.yaml
```

Head over to https://stackrox.example.org

You would find the `admin` password under `./deploy/k8s/central-deploy/password`

Success!

![](https://blog.wains.be/images/stackrox.png)

If you want to uninstall:

```bash
kubectl delete podsecuritypolicy stackrox-central
kubectl delete podsecuritypolicy stackrox-scanner
kubectl delete clusterrole stackrox-central-psp
kubectl delete clusterrole stackrox-scanner-psp
kubectl delete ns stackrox
```