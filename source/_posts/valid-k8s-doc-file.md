---
title: How to valid your K8S manifest file
date: 2021-12-30 13:52:11
category: k8s
tags:
- k8s
---

`kubectk apply / create` with `--dry-run` option, you can check whether manifest file is correctly or not without without persisting the resource into your cluster.
`--dry-run` has three options: `none`, `server`, `client`. I will try to run with these options.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        # Correct: - containerPort: 80
        - containerPort: -1
          name: nginx
        resources:
          limits:
            memory: "128Mi"
            cpu: "500m"
```

- dry-run: none (default)
```sh
sh-3.2$ kubectl apply -f nginx-deployment.yaml --dry-run=none
The Deployment "nginx" is invalid: spec.template.spec.containers[0].ports[0].containerPort: Invalid value: -1: must be between 1 and 65535, inclusive
```

- dry-run: client
```sh
sh-3.2$ kubectl apply -f nginx-deployment.yaml --dry-run=client
deployment.apps/nginx created (dry run)
```
 If client strategy, only print the object that would be sent, without sending it.
 So you don't know the syntax of manifest file is correct or not.

- dry-run: server
```sh
sh-3.2$ kubectl apply -f nginx-deployment.yaml --dry-run=server
The Deployment "nginx" is invalid: spec.template.spec.containers[0].ports[0].containerPort: Invalid value: -1: must be between 1 and 65535, inclusive
```
If server strategy, submit server-side request without persisting the resource.
The syntax of manifest file will be checked.