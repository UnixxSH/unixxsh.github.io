---
layout: default
title: Commands
parent: Kubernetes
nav_order: 1
---

# Commands

___

## Purge not running pods
```bash
kubectl delete pods -A --field-selector=status.phase=Failed --field-selector=status.phase=Succeeded
```

___

## exec as user
```bash
kubectl auth can-i get pods --as=fatuser
```

___

## Purge notready pods (crictl)
```bash
for i in `crictl pods | grep NotReady | awk '{print $1}'`; do crictl rmp $i; done
```

___

## Force namespace deletion
```bash
kubectl get namespace <ns> -o json > <ns>.json
```
```bash
kubectl replace --raw "/api/v1/namespaces/<ns>/finalize" -f ./<ns>.json
```

___

## Allow pods to communicate with firewalld
```bash
firewall-cmd --permanent --new-zone=<name>
```
```bash
firewall-cmd --permanent --zone=<name> --set-target=ACCEPT
```
```bash
firewall-cmd --permanent --zone=<name> --add-source=<cluster_pods_cidr>
```

___

## containerd push registry
```bash
ctr content fetch --platform linux/amd64 docker.io/library/postgres:15.1-alpine
```

```bash
ctr images tag docker.io/library/postgres:15.1-alpine localhost:32000/postgres:15.1-alpine
```

```bash
ctr images push --platform linux/amd64 localhost:32000/postgres:15.1-alpine
```

___

## Get the owner of all pods
```bash
kubectl get pods -A -o jsonpath='{range .items[*]}{.metadata.name} {.metadata.ownerReferences[].kind}{"\n"}{end}'
```
