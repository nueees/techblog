---
toc: false
layout: post
description: 5장-7장
categories: [kubernetes]
title: Kubernetes
---

출처\_Building Cloud private native 전문가 양성과정 교재


# 5.Container Cluster

## 5.1.Kubernates 소개

docker - 단일 시스템에서만 다수의 컨테이너 관리  
\-> 다수의 시스템과 애프리케이션 설정을 쉽게 설정하고 유지보수할 수 있는 방식인 오케스트레이션(Ochestration)이 필요  
kubernates - 구글에서 개발된 컨테이너 오케스트레이션 도구

**_다중 컨테이너 관리를 위한 docker-compose를 설치해야 함._**

쿠버네티스는 클러스터 구성해서 오케스트레이션을 통해 컨테이너를 자동으로 관리, 2개 이상 시스템에서 관리 가능  
관리 대상을 object라고 함 - pods(컨테이너 단위)와 controller(pods를 한번에 관리)로 구성(application workload)

### 기능

컨테이너 플랫폼, 마이크로서비스 플랫폼, 이식성있는 클라우드 플랫폼 제공  
- Automatic Binpacking  
- Storage Orchestration  
- Secret & Configuration Management  
- Horizontal Scaling  
- Service Discovery & Load Balancing  
- Self Healing  
- Batch Execution  
- Automatic Rollbacks & Rollouts  


CI/CD 파이프라인, 애플리케이션 레벨의 서비스, 로깅, 모니터링, 경고 솔루션 등을 제공하지 않음

---

### 사전 설치

1.  choco 패키지 관리도구 설치
2.  `$ Set-ExecutionPolicy Bypass -Scope Process -Force; \[System.Net.ServicePointManager\]::SecurityProtocol = \[System.Net.ServicePointManager\]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('[https://community.chocolatey.org/install.ps1'](https://community.chocolatey.org/install.ps1')))`
3.  vagrant 설치혹은 vagrant.exe 파일 다운로드 후 설치 가능
4.  `$ choco install vagrant`
5.  하이퍼바이저 설치  
    virtualbox 설치 (확장팩)
6.  vagrant 명령어로 구성요소 설치  
    vagrant plugin install vagrant-hostmanager  
    vagrant plugin install vagrant-disksize  
    확인: vagrant plugin list윈도우의 경우 직접 작성하면 확장자이름이 자동설정될 수 있으니 주의
7.  `$ vagrant box add ubuntu\/bionic64 $ vagrant up => Vagrantfile 이 있는 곳에서 실행`
8.  가상머신에 쿠버네티스 배포  
    ssh 설정
9.  `$ ssh-keygen $ ssh-copy-id node1 (노드 모두 입력) localhost 도 설정`

---

### kubenates 설치

```
1.  패키지 및 git 설치

$ sudo apt update $ sudo apt upgrade -y $ sudo apt install -y python3 python3-pip git $ git clone --single-branch --branch release-2.14 https://github.com/kubernetes-sigs/kubespray.git $ cd kubespray/ $ sudo pip3 install -r requirements.txt`

2.  인벤토리 수정

$ cp -rfp inventory/sample/ inventory/mycluster $ vim inventory/mycluster/inventory.ini [all] node1 ansible_host=192.168.56.21 ip=192.168.56.21 node2 ansible_host=192.168.56.22 ip=192.168.56.22 node3 ansible_host=192.168.56.23 ip=192.168.56.23 controll-plane ansible_host=192.168.56.11 ip=192.168.56.11 [all:vars] ansible_python_interpreter=/usr/bin/python3 [kube-master] controll-plane [etcd] controll-plane [kube-node] node2 node3 node1 [calico-rr] [k8s-cluster:children] kube-master kube-node calico-rr`

$ sudo vim /etc/hosts
192.168.56.11   controll-plane.example.com controll-plane
192.168.56.21   node1.example.com node1
192.168.56.22   node2.example.com node2
192.168.56.23   node3.example.com node3

$ vim inventory/mycluster/group_vars/k8s-cluster/addons.yml
metrics_server_enabled: true
ingress_nginx_enabled: true
metallb_enabled: true
metallb_ip_range:
  - "192.168.56.50-192.168.56.99"
metallb_protocol: "layer2"

$ vim inventory/mycluster/group_vars/k8s-cluster/k8s-cluster.yml
kube_proxy_strict_arp: true

3.  플레이북 실행
$ ansible-playbook -i inventory/mycluster/inventory.ini cluster.yml -b`

4.  kubectl 설치
$ curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl" $ curl -LO "https://dl.k8s.io/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256" $ echo "$(<kubectl.sha256) kubectl" | sha256sum --check $ sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl $ kubectl version --client`
```

---

## 5.1.Architecture

![]({{site.baseurl}}/images/post/docker_5_1.jpg)

kube-api-server를 통해서 대부분의 작업을 함  
kube-controller-manager : on-premise 환경에서 각 리소스 관리  
cloud-controller-manager : cloud 환경에서 밴더사 자체적으로 관리

### 마스터(master)

cluster를 구성하기 위한 핵심 요소들의 모음  
cluster 조정을 위한 control plane 제공  
노드에 작업 분배하는 scheduling 작업  
cluster evnet 감지하고 대응  
운영환경에서는 multi master 환경을 구성 (FT)

1) API 서버(kube-apiserver)  
kubernetes API를 노출하는 component
kubernetes object 관리, 제어를 위한 front-end

2) etcd  
cluster의 meta 정보 정장  
key-value 형태로 저장  
cluster configuration 정보 보관하고 있으므로 백업 필수

3) scheduler  
cluster 내 생성되는(혹은 배정되지 않은) pod를 감지하고 그것을 구동할 노드를 선택  
resourse 상태, HW/SW/Policy 제약, Affinity 등 다양한 기준에 따라 배치 결정

4) cube-controller-manager

-   node controller: 노드 관리, 노드 다운시 대응
-   replication controller: 복제 컨트롤러를 사용하는 모든 object관리, 적정 pod 수 유지
-   endpoint controller: service와 pod 연결 (물리적 시스템과 컨테이너 내부 어플리케이션 연결)
-   service account & token controller: 쿠버네티스 namespace, account, token 등 인증 관련된 것 담당

5) cloud-controller-manager

AWS, GCP 등 각 밴더 별 클라우드 서비스 관리  

-   node controller: 노드 관리, 노드 다운시 대응
-   route controller: 클라우드 환경의 네트워크 경로
-   service controller: 클라우드 로드밸런서 관리
-   volume controller: 클라우드 볼륨 관리

### 워커 노드(worker node)
container를 실행하고 동작중인 Pods 유지시키고 kubernetes runtime 환경 구성 (minion이라고도 함)

1) kubelet  
각 노드에서 실행되는 agent로 마스터로부터 제공받은 구성 정보, 노드가 수행해야할 작업 동작

2) kube-proxy  
네트워크 규칙 유지하고 연결에 대한 포워딩 수행하므로써 서비스 추상화 가능하게 함
컨테이너에 연결될 네트워크 구성 관리

3) container runtime  
실제 컨테이너 동작을 책임지는 구성요소

-   Docker
-   containerd
-   CRI-O
-   rktlet
-   Kubernetes CRI(Container Runtime Interface)

### 추가 요소(add-on)

| 구성요소 | 설명 |
| :-: | :-- |
| 클러스터 DNS | 쿠버네티스 클러스터 내의 여러 오브젝트에 대하여 주소기반으로 오브젝트를 접근할 수 있는 DNS 제공 |
| DashBoard | 모니터링 용 웹기반 인터페이스 |
| 컨테이너 resource 모니터링 | 컨테이너 리소스 사용량의 시계열 매트릭스 기록 |
| 클러스터 logging | 컨테이너 로그를 중앙로그저장소에 저장하고 관리, 로그를 검색/열람하는 인터페이스 제공 |

### 쿠버네티스 API

1) API 버전 규칙  
alpha 버전 API : 개발 초기 버전  
beta 버전 API : 일정 수준 이상 테스트가 된 버전  
stable 버전 : vX 형태의 안정화된 버전

2) API 그룹  
core group: apiVersion:\[version\]  
core 이외 group: apiVersion: \[group\]/\[version\]

---

## 5.3.Menifest

| 필드 | 설명 |
| :-: | :-- |
| apiVersion | object를 생성하기 위한 API 버전을 지정 |
| kind | object 종류 |
| metadata | name, label, namespace 등 기본정보 |
| spec | object 세부 상태 |

## Managing Object

1) Imperative commands(명령형 커맨드)  
재사용성이 떨어져 개발 환경에서 테스트 시, 일회성 작업일 경우 사용

```
$ kubectl run nginx --image nginx
```

2) Imperative object configuration(명령형 오브젝트 구성)  
yaml, json 포맷으로 파일을 작성하여 kubectl은 작성된 파일을 참고하여 실행

```
$ kubectl create -f nginx.yaml
```

3) Declarative object configuration(선언형 오브젝트 구성)  
특정 디렉토리에 오브젝트 파일을 배치하고 작성된 파일을 참고하여 오브젝트 관리

```
$ kubectl diff -f configs/
$ kubectl apply -f configs/
```

## Running App with Imperative commands 예시

```
$ kubectl run mytest-app --image=<ACCOUNT>/myweb --port=8080 --generator=run/v1 # 레플리케이션 컨트롤러(pod) 생성
$ kubectl get pods
$ kubectl get replicationcontrollers
$ kubectl expose replicationcontroller mytest-app --type=LoadBalancer --name myweb-svc # 서비스 생성
$ kubectl get services
$ curl http://192.168.56.11:31289
$ kubectl scale replicationcontroller mytest-app --replicas=3
$ kubectl get pods
$ kubectl get replicationcontrollers
$ curl http://192.168.56.11:31289 
$ kubectl get all
$ kubectl delete replicationcontrollers mytest-app
$ kubectl delete service myweb-svc
```

---

# 6.Application Workload

## 6.1.Pods

도커에서 작업을 수행하기 위해 구동해야 하는 가장 작은 단위는 컨테이너 
쿠버네티스 클러스터 내에서 애플리케이션 배포하며 동작하는 단위   
1 컨테이너 = 1 애플리케이션  
하나의 Pod는 하나의 node에서만 동작    
동일한 Pod 의 모든 컨테이너는 동일한 리소스와 로컬 네트워크를 공유하여 머신이 분리되어 있어도 pod 내 컨테이너 간
통신이 가능함  
쿠버네티스 클러스터는 여러 개의 노드로 구성되며 각 노드는 컨테이너를 구동할 수 있도록 준비하고 있으나,  
하나의 파드에 두개 이상의 컨테이너가 포함된 경우 각 컨테이너를 여러 노드에 분산시켜서 실행할 수는 없음.  
1 pod 내 있는 컨테이너는 저장소, 네트워크 IP 등 공유

1.  Pod 정의: YAML 파일 생성

cat testapp-pod.yml

![]({{site.baseurl}}/images/post/docker_6_1_1.jpg)
  
기본적인 apiVersion, kind, metadata, spec 포함  
\- 하이픈 기호는 리스트(List)를 의미

2.  Pod 생성 및 확인
3.  `$ kubectl create -f testapp-pod.yml $ kubectl get pods $ kubectl get pods testapp-pod -o yaml(-o json) $ kubectl discribe pods testapp-pod $ kubectl logs testapp-pod # 로그 확인 $ kubectl port-forward testapp-pod 8080:8080 # 포트포워딩 $ curl http://localhost:8080`
4.  Label(레이블) 및 Selector(셀렉터)  
    Label: 쿠버네티스 클러스터의 모든 오브젝트에 키/값 쌍으로 이루어진 값을 설정하여 리소스 식별, 속성 지정 역할  
    네임스페이스 내 중복 불가  
    Label Selector: Label을 식별하고 검색함

cat testapp-pod-label.yml


![]({{site.baseurl}}/images/post/docker_6_1_2.jpg)

4.  Anotation(어노테이션)  
    오브젝트의 추가 정보를 기록하는 경우 사용하는 주석
5.  `$ kubectl annotate pods testapp-pod devops-team/developer="nueees"`
6.  Name Space(네임스페이스)  
    Name Space: 쿠버네티스 클러스터 내 오브젝트와 리소스를 용도와 목적에 따라 논리적으로 완전히 분리된 환경default: 기본 네임스페이스  
    kube-node-lease: 쿠버네티스 노드(마스터/노드)의 가용성 체크를 위한 네임스페이스  
    kube-public: 모든 사용자 접근가능  
    kube-system: 클러스터의 리소스가 배치되는 네임스페이스
7.  `$ kubectl get namespaces`
8.  Liveness Prove(라이브니스 프로브)  
    파드 상태가 정상적인지 주기적으로 모니터링 서비스

-   HTTP GET Prove: 특정 경로에 HTTP GET 요청, HTTP 응답코드가 2XX/3XX인지 확인
-   TCP Socket Prove: 특정 TCP port 연결 시도
-   Exec Prove: 컨테이너 내부의 바이너리(명령)를 실행하고 종료 코드 확인  
    cat testapp-pod-liveness.yml  

![]({{site.baseurl}}/images/post/docker_6_1_3.jpg)    
    ```
    $ kubectl create -f testapp-pod-liveness.yml
    ```
    

---

## 6.2.Controller

사용자가 의도한 상태로 유지 해 주는 기능

### Deployment
stateless Application 배포 시 사용  
Applicaion은 컨테이너 집합인 Pod 단위로 배포  
사용자의 기대상태(Desired state)를 유지하도록 하는 controller  
 - Liveness Probe: 응답 체크
 - Readlness Probe: 서비스 가능 상태 체크
ReplicaSet에 대한 Update 담당  

use case:
- 신규 ReplicaSet을 생성하여 Pod를 새로운 버전으로 점진적 교체 수행 (Rolling Update)
- Application configuration 분리 (Decoupling): Config Map


### ReplicaSet (레플리카셋)

사용자가 요구하는 복제본 개수만큼 Pod를 복제하고 관리하는 기능  
주로 Deployment 의 spec 으로 정의하는 것을 권장함   
관리해야 하는 pod을 식별하기 위한 selector, 유지해야 하는 pod의 개수, pod template 포함  

-   Pod의 다중 레이블 조건 지원
-   Pod에 설정된 레이블의 키 조재 여부 조건 선택 가능

cat testapp-rs.yml


![]({{site.baseurl}}/images/post/docker_6_2_1.jpg)   

```
$ kubectl create -f testapp-rs.yml
$ kubectl get replicasets.apps
```

cat testapp-rs-exp.yml


![]({{site.baseurl}}/images/post/docker_6_2_2.jpg)   

```
$ kubectl create -f testapp-rs-exp.yml
$ kubectl get replicasets.apps -o wide
```

### DaemonSet (데몬셋)

쿠버네티스 클러스터는 부하 분산, 이중화를 통한 장애 대응 목적 등을 위하여 최소 하나 이상의 노드로 구성되므로  
다수의 노드를 사용할 경우 필요에 따라 _각 노드별\*로 특정 목적을 수행하는 \*파드를 한 개씩_ 배치하여야 하는 경우 발생

**레플리카셋과 비슷하지만 복제본을 지정하지 않음**

cat testapp-ds.yml


![]({{site.baseurl}}/images/post/docker_6_2_3.jpg)   
  
nodeSelector에 Pod가 배포될 노드 선택

```
$ kubectl create -f testapp-ds.yml
$ kubectl get replicasets.apps -o wide
$ kubectl label nodes kube-node1 node=development # node에 label지정
$ kubectl label nodes kube-node1 --show-label
```


### StatefulSet
Pod이 스케줄 될 때 지속적으로 유지되는 식별자를 가질 수 있도록 관리하는 object
use case: 고유한 네트워크 식별자, 지속성을 갖는 스토리지(persistent volumes), 순차적 배포와 스케일링, 순차적인 자동 


---

# 7.Network - Service

## 7.1. Service 생성

### Service: 쿠버네티스 시스템에서 같은 애플리케이션을 실행하도록 구성된 컨트롤러에 의해 생성된 Pod 그룹에 단일 네트워크 진입점 제공

- 서비스에 부여된 IP는 해당 서비스가 종료될 때까지 유지하고 클라이언트는 이 서비스에 부여된 고정 IP 및 PORT를 통해 Pod에 접근 가능  
- 클러스터 내 Pod들에게 접근하기 위한 방법으로 사용  
- 여러 Pod를 묶어 Healthy한 Pod로 Traffic 라우팅하는 로드 밸런싱 기능 제공  
- 클러스터의 Service CIDR 중에서 지정된 IP로 생성 가능  
- 서비스 이름은 클러스터내 고유한 DNS로 동작  

cat testapp-svc.yml


![]({{site.baseurl}}/images/post/docker_7_1_1.jpg)   

```
$ kubectl create -f testapp-svc.yml
$ kubectl get services # 진입점인 서비스
$ kubectl get endpoints testapp-svc # 엔드포인트 -> 레플리카셋 컨트롤러의 파드
```

엔드포인트: 최종 목적지인 파드의 주소 및 포트 정보

```
$ kubectl run nettool -it --image=\<ACCOUNT\>/network-multitool # 서비스 접근 테스트
```

### Session Affinity: 클라이언트가 특정 파드(웹서비스) 요청 시 이전에 처리된 파드로 동일하게 전달하여 처리

cat testapp-svc-ses-aff.yml


![]({{site.baseurl}}/images/post/docker_7_1_2.jpg)   

```
$ kubectl create -f testapp-svc-ses-aff.yml
```

### Configuring Service Multi-Port

Pod는 하나 이상의 컨테이너로 구성되어 있어서, 각 컨테이너는 서로 다른 포트 사용 가능

cat testapp-svc-multiport.yml


![]({{site.baseurl}}/images/post/docker_7_1_3.jpg)   

```
$ kubectl create -f testapp-svc-multiport.yml
```

### Configuring Service by named-port

레플리카셋 컨트롤러의 Pod 템플릿에서 생성될 컨테이너의 포트에 이름을 부여하여 포트 이름 구성 가능

cat testapp-rs-named-port.yml


![]({{site.baseurl}}/images/post/docker_7_1_4.jpg)   

  
cat testapp-svc-named-port.yml


![]({{site.baseurl}}/images/post/docker_7_1_5.jpg)  

```
$ kubectl create -f testapp-rs-named-port.yml -f testapp-svc-named-port.yml
```

## 7.2. Service 탐색

kubectl get 명령어로 IP 주소를 수동적으로 확인할 수 있지만 Object끼리 통신을 위한 방식 필요

1.  환경변수 방식
2.  쿠버네티스 클러스터 내 DNS 사용 방식

### DNS를 이용한 Service 탐색

kube-systme 네임스페이스에서 쿠버네티스에 등록된 구성요소 확인 가능  
그중 _k8s-app=kube-dns_ 레이블 옵션을 통해 DNS 관련 파드 확인

1) DNS 관련 리소스 확인

```
$ kubectl get all -n kube-system -l k8s-app=kube-dns
```

2) 파드 내부 DNS 설정 확인  
이름 기반의 주소로 네트워크에 접근하기 위해서 DNS 설정 필요

```
$ kubectl exec testapp-rs-m65m4 -- cat /etc/resolv.conf # 위의 coredns 서비스 IP와 다름
```

각 파드의 DNS로 등록되어 있는 위 IP(169.254.0.0/16 형식)는 IPv4 주소형식에서 'Link Local Address'이며, 유효한 IP 주소가 아님  
3) NoceLocal DNSCache  
해당 주소(169.254.25.10)는 coreDNS로 직접적으로 요청이 전달되지 않게 하는 중간 단계 캐시 DNS  
DaemonSet 형태로 쿠버네티스의 각 노드마다 DNS 캐시 기능을 하는 Pod 배치하여 필요한 경우에만 CoreDNS 호출하는 구조

### Pod <-> NodeLocal DNSCache <-> iptables <-> coreDNS

```
$ kubectl get daemonsets.apps -l k8s-app=kube-dns -n kube-system # 각 노드별 데몬셋 컨트롤러 확인
$ kubectl get pods -l k8s-app=kube-dns -n kube-system | grep -A 2 Args # node local dns 확인

$ kubectl run nettool -it --image=\<ACCOUNT\>/network-multitool --generator=run-pod/v1 --rm=true bash # 서비스 접근 테스트
```

주소구성: <리소스(서비스) 이름>.<네임 스페이스>.<리소스 종류>.<클러스터 도메인>

## 7.3. Service 종류

위의 내용은 내부 접근이고, 외부 접근 제공하는 서비스 구성 필요

### ClusterIP: 클러스터 내부용 진입점 제공

### NodePort: 쿠버네티스 각 노드(호스트)의 포트를 외부 접근용으로 할당

### LoadBalancer: NodePort의 확장판으로, 외부 로드밸러서로 접근하면 서비스를 통해 파드로 Redirection

### ExternalName: 외부에서 접근하기 위한 서비스 유형이 아닌, CNAME 매핑을 통해 특정 FQDN과 통신을 위한 기능

1.  외부 접근용 레플리카셋 생성 및 확인
2.  `$ kubectl create -f testapp-rs.yml $ kubectl get replicasets.apps $ kubectl get pods`
3.  NodePort 서비스 생성  
    cat testapp-svc-ext-nodeport.yml  
![]({{site.baseurl}}/images/post/docker_7_3_1.jpg)  
4.  `$ kubectl create -f testapp-svc-ext-nodeport.yml # 해당 노드에서 사용할 포트 31111로 지정 $ kubectl get endpoints testapp-svc-ext-np # 서비스의 엔드포인트 확인 (Pod의 8080 포트로 Redirection 됨) $ kubectl get nodes -o wide # 각 노드의 IP 확인`
5.  LoadBalancer 서비스 생성  
    cat testapp-svc-ext-loadbalancer.yml  
![]({{site.baseurl}}/images/post/docker_7_3_2.jpg)  
6.  `$ kubectl create -f testapp-svc-ext-loadbalancer.yml # 해당 노드에서 사용할 포트는 정의하지 않음 $ kubectl get services # LoadBalancer 서비스 확인`
7.  ExternalName 서비스 생성  
    cat testapp-svc-ext-externalname.yml  
![]({{site.baseurl}}/images/post/docker_7_3_3.jpg)  
8.  `$ kubectl run nettool -it --image=\<ACCOUNT\>/network-multitool --generator=run-pod/v1 --rm=true bash # 서비스 접근 테스트 $ nllookup testapp-svc-extname-gl`
9.  `$ kubectl create -f testapp-svc-ext-externalname.yml # FQDN은 google이며 이에 대한 CNAME은 testapp-svc-extname-gl $ kubectl get services # ExternalName 서비스 확인`

---


