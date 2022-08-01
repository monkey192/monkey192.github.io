---
title: k8s-volume
date: 2022-07-26 09:48:44
tags: k8s
---

### Contents
- Volumes in Kubernetes
- What differences between `volume` and `persistenceVolume`

Refer: [Types of Kubernetes Volumes](https://cloud.netapp.com/blog/cvo-blg-5-types-of-kubernetes-volumes-and-how-to-work-with-them)

### Volumes in Kubernetes
Containers are ephemeral by design, it means that anything which is stored in container will be lost when the container is stopped.
The might cause proplems with containers that need to persist their data
(eg: database, filestorage ...)

In k8s, a volume can be thought as a directory which is accesscible to the containers in a pod/
