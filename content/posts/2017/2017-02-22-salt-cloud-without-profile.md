---
date: 2017-02-22
title: "Create VM with Salt Cloud without profile files"
---

You read everywhere (and especially [here](https://docs.saltstack.com/en/latest/topics/cloud/basic.html#creating-a-vm)) in Salt documentation that you need a profile file to create virtual machines in one of the supported clouds.

I found this approach not to be very dynamic.

It is actually not necessary to have profiles. There is a [cloud runner](https://docs.saltstack.com/en/latest/ref/runners/all/salt.runners.cloud.html), with a function `create`, dedicated to creating virtual machines. You can pass VM specifications in a pillar.

Your orchestration state `/srv/salt/states/orch_vmware/createvm.sls`:

```{% from "maps/salt.yaml" import maps_salt with context %}{% from "maps/proxy.yaml" import maps_proxy with context %}{% from "maps/resolver.yaml" import maps_resolver with context %}# Retrieving VM specs{% set data = salt.pillar.get('event_data') %}# VM creationcreatevm:  salt.runner:    - name: cloud.create    - provider: vmware    - instances: {{ data.post['instances'] }}    - clonefrom: {{ data.post['clonefrom'] }}    - datastore: {{ data.post['datastore'] }}    - num_cpu: {{ data.post['num_cpu'] }}    - memory: {{ data.post['memory'] }}    - datacenter: {{ data.post['datacenter'] }}    - folder: {{ data.post['folder'] }}    - cluster: {{ data.post['cluster'] }}    - ssh_username: {{ data.post['ssh_username'] }}    - password: {{ data.post['password'] }}    - devices:       network:         Network adapter 1:           name: {{ data.post['vlan'] }}           adapter_type: vmxnet3           ip: {{ data.post['ip'] }}           subnet_mask: {{ data.post['subnet_mask'] }}           gateway: {{ data.post['gateway'] }}           switch_type: standard    - domain: {{ maps_resolver['search'] }}    - dns_servers: {{ maps_resolver['dns'] }}    - minion:       master: {{ data.post['master'] }}    - sync_after_install: all    - display_ssh_output: False    - script: bootstrap-salt    - script_args: -H {{ maps_proxy['proxy'] }} stable {{ maps_salt['upstream-version'] }}.{{ maps_salt['upstream-version-patch'] }}```

Instead of using the command `salt-cloud`, we use `salt-run` like this:

```
salt-run state.orchestrate orch_vmware.createvm \ 
	pillar="{event_data: {post: {master: 'master', instances: 'vmname', \
	clonefrom: 'redhat7', datastore: 'datastore1', num_cpu: 2, memory: 4096, \
	datacenter: 'level3', cluster: 'cluster1', vlan: 'VLAN10', ip: '192.168.1.10', \
	subnet_mask: '255.255.255.0', gateway: '192.168.1.1', ssh_username: 'root', \
	password: 'example'}}}"
```
