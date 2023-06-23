---
toc: false
layout: post
description: Certified Kubernetes Administrator
categories: [kubernetes]
title: Certified Kubernetes Administrator
---

[따배씨](https://www.youtube.com/watch?v=dv_5WCYS5P8&list=PLApuRlvrZKojqx9-wIvWP3MPtgy2B372f&index=4)

---


# 1. Control Plane

## 1.1. ETCD Backup&Restore

```
ssh k8s-master
etcd --version
etcdctl version
```

### [snapshot using etcdctl options](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#snapshot-using-etcdctl-options)
```
] sudo ETCDCTL_API=3 etcdctl \
  --endpoints=https://127.0.0.1:2379 \
  --cacert=<trusted-ca-file> \
  --cert=<cert-file> --key=<key-file> \
  snapshot save <backup-file-location>
Snapshot saved at <backup-file-location>
```

### [restoring an etcd cluster](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#restoring-an-etcd-cluster)
```
] sudo ETCDCTL_API=3 etcdctl snapshot restore \
  --data-dir <new-data-dir-location> <backup-file-location>
```
```
] sudo vi /etc/kubernetes/manifests/etcd.yaml
volumes:
  - hostPath:
    path: /var/lib/<new-data-dir-location> # edit
] sudo docker ps -a | grep etcd
```


---
## 1.2. Creating Pod


---
## 1.3. Creating Static Pod


---
## 1.4. Creating Multi-container Pod


---
## 1.5. Creating Side-car container


---
## 1.6. Deployment & Pod Scale


---
