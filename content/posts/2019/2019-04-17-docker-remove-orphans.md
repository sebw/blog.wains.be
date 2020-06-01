---
date: 2019-04-17
title: "Remove Docker orphans"
---

## Orphan volumes

List mountpoints that are not in use:

```
for j in $(for i in $(docker volume ls -qf dangling=true)
do
docker volume inspect $i | grep "Mountpoint" | awk -F':' '{print $2}' | awk -F'"' '{print $2}'
done)
do
du -h --max-depth=1 $j
done
```

Now feel free to remove folder that you considered not needed anymore.

## Orphan images

Removing unused images is possible directly from the Docker command:

```
$ docker image prune -a
WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N] y
Deleted Images:
[...]
deleted: sha256:c2c40a3a65716c73c3931a7933183779c643cd0af858a99f0c3628f96306ec49
deleted: sha256:90c4aaeca6c08376a180443c2e151026416124d48aeb60ebddd7f456507078e4
deleted: sha256:d3e4034ae956112e97e14bfff0ab16ba040a20ecd7741bdc5f189a45ac61d0e6
deleted: sha256:5d50d30f8b0711be7d34c7ffc904764a92f99ae99b91699810e1a9669f984942
deleted: sha256:5779fb68ae965e79e27575feb056c6ca6a03660ccab549d5e1a3c54519b0161b

Total reclaimed space: 13.33GB
```
