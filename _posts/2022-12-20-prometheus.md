---
toc: false
layout: post
description: 4
categories: [prometheus]
title: Prometheus
---

[실습으로-배우는-프로메테우스](https://github.com/SeongJuMoon/_Lecture_prom_learning.kit)

---


# 4. Prometheus Exporter

## 4.1. Service discovery (수집 대상)

configuration tap의 job_name의 kubernetes_sd_configs(수집 대상)을 통해 target을 생성
```
global:
  ...
rule_files:
  ...
scrape_configs:
- job_name: prometheus
  ...
- job_name: kubernetes-apiservers
  ...
  kubernetes_sd_configs:
  - role: endpoints
    ...
- job_name: kubernetes-nodes
  ...
  kubernetes_sd_configs:
  - role: node
    ...
- job_name: kubernetes-nodes-cadvisor
  ...
  kubernetes_sd_configs:
  - role: node
    ...
- job_name: kubernetes-service-endpoints
  ...
  kubernetes_sd_configs:
  - role: endpoints
    ...
- job_name: kubernetes-service-endpoints-slow
  kubernetes_sd_configs:
  - role: endpoints
    ...
- job_name: prometheus-pushgateway
  ...
  kubernetes_sd_configs:
  - role: service
    ...
- job_name: kubernetes-services
  ...
  kubernetes_sd_configs:
  - role: service
    ...
- job_name: kubernetes-pods
  ...
  kubernetes_sd_configs:
  - role: pod
    ...
- job_name: kubernetes-pods-slow
  ...
  kubernetes_sd_configs:
  - role: pod
    ...
```


## 4.2. cAdvisor(container monitoring)
각 container 내의 Kubelet을 통해 메트릭 수집  
![image](https://user-images.githubusercontent.com/83441376/208759712-49d03556-90a2-45ef-8cd0-69806be689cb.png)


graph tap에서 container로 prefix주고 검색하는 모든 항목이 cAdvisor를 통해 수집된 데이터임. 
```
container_network_receive_bytes_total{pod="pod-name"}
```



## 4.3. node-exporter(node monitoring)
각 node(os 관련)의 메트릭 수집 
```
ls /sys/fs/cgroup # sys 정보
] blkio  cpuacct      cpuset   freezer  memory   net_cls,net_prio  perf_event  systemd
  cpu    cpu,cpuacct  devices  hugetlb  net_cls  net_prio          pids

cat /proc/version | cat /proc/cpuinfo | cat /proc/meminfo # proc 정보
] Linux version 3.10.0-1127.19.1.el7.x86_64
] processor       : 0
  model name      : 11th Gen Intel(R) Core(TM) i7-1165G7 @ 2.80GHz
  cpu cores       : 2
  ...
] MemTotal:        2046800 kB
  MemFree:           89764 kB
  MemAvailable:     903108 kB
  Buffers:            1036 kB
  Cached:           919464 kB
  ...
```

graph tap에서 node로 prefix주고 node name 넣으면 node os 관련 수집된 데이터임. 
```
node_memory_Active_bytes{node="w3-k8s"}
node_cpu_seconds_total{node="w3-k8s", mode="user"}
```

### 4.3.1. cgroup  

process에 할당하는 CPU, memory, network bandwidth와 같은 자원을 개별적으로 제한하기 위한 system kirnel의 기능  
container runtime은 systemd 혹은 cgroupfs으로 cgroup 자원을 할당  
```
docker info | grep Cgroup -F2 # 명령어로 cgroup 정보 확인가능
] Cgroup Driver: systemd
```

### 4.3.2. cgroup - cgroupfs  
/sys/fs/cgroup 경로에서 파일시스템 형태로 resource를 직접 매핑하는 구조로 관리  
```
tree /sys/fs/cgroup
] /sys/fs/cgroup
  |-- blkio
  |   |-- blkio.io_merged
  |   |-- blkio.io_merged_recursive
  |   |-- kubepods.slice
  |   |   |-- blkio.io_merged
  |   |   |-- blkio.io_merged_recursive
  ...
```
docker에서 사용(systemd로 변경 예정)  

### 4.3.2. cgroup - systemd  
slice > scope > service 단위의 계층 구조로 slice의 이름으로 된 파일 시스템 구조로 관리    
/sys/fs/cgroup/systemd 경로에서 계층 구조 확인 가능  
```
systemd-cgls
] ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
  ├─user.slice
  │ └─user-0.slice
  │   └─session-4.scope
  │     ├─13329 sshd: root@pts/0
  └─system.slice
    ├─systemd-logind.service
    │ └─32496 /usr/lib/systemd/systemd-logind
    ├─kubelet.service
    │ └─4333 /usr/bin/kubelet --bootstrap-kubeconfig=/etc/kubernetes/bootstrap-kubelet.conf --kubeconfig=/etc/kubernete
    ├─docker.service
    │ └─3437 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
    ├─containerd.service
    │ ├─ 3428 /usr/bin/containerd
    │ ├─ 4013 /usr/bin/containerd-shim-runc-v2 -namespace k8s.io -id 9aa76cbf6daf29adb4140d548b086dc0ee96325ed455335af5
    │ ├─kubepods-besteffort-pod068e1c1b_4b04_4ee0_a9a5_91b79efc8447.slice:cri-containerd:ef8138f99e53bffaf9fcef4ac44839
    │ │ └─1555 /speaker --port=7472 --log-level=info
    │ ├─kubepods-burstable-podd5af555b67b9595de495fa4320f406a7.slice:cri-containerd:01360ee58caed091cf04b20aaf684a0fae8
    │ │ └─1356 kube-controller-manager --allocate-node-cidrs=true --authentication-kubeconfig=/etc/kubernetes/controlle
```
k8s에서 default 사용  


## 4.4. kube-state-metrics(cluster monitoring)
control plane에서 kube-api-server를 통해 kubenates object status의 메트릭 수집  
![image](https://user-images.githubusercontent.com/83441376/208758030-bcf29991-dc08-4284-8e15-d83fb50a2057.png)



graph tap에서 kube로 prefix주고 검색하면 kube-state-metrics로 수집된 데이터임.  
```
$ kubectl scale deploy deploy-app --replicas 6 # scale out deployment 후 replica된 pod 확인 가능
kube_deployment_status_replicas_available{deployment="deploy-app"}
```


## 4.5. application 전용 exporter(application monitoring)
4.2. cAdvisor(container monitoring)과 비슷하나, 각 application에서 수집하고자 하는 추가된 adapter(or sidecar) container 전용 메트릭 e.g. HTTP request

graph tap에서 application(nginx)로 prefix주고 검색하면 해당 컨테이너로 수집된 데이터임.  
```
nginx_up
nginx_http_requests_total
```

deployment-nginx-exporter.yaml (nginx+exporter 컨테이너 두개)
```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      annotations:
        # config default exec/logs connectivity for main nginx 
        kubectl.kubernetes.io/default-container: nginx
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx:1.21
        name: nginx
        volumeMounts:
        - name: nginx-vol
          mountPath: /etc/nginx/templates
        env:
        - name: PORT
          value: '8080'
      - image: nginx/nginx-prometheus-exporter:0.10.0
        name: exporter
        ports:
        - containerPort: 9113
        args:
        - -nginx.scrape-uri=http://localhost:8080/stub_status # 8080 port의 nginx 데이터 긁어 옴
```


## 4.6. about embeded application(other monitoring)
역시 4.5. application 전용 exporter(application monitoring)와 비슷하고 이미 구현되어 있는 메트릭들  

graph tap에서 application(metallb)로 prefix주고 검색하면 해당 컨테이너로 수집된 데이터임.  
```
metallb_allocator_addresses_in_use_total
```


---
