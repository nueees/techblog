---
toc: true
layout: post
description: 3장
categories: [kubernetes]
title: Docker Image Build
---
<!-- ![]({{site.baseurl}}/images/post/1_1.jpg) -->

출처\_Building Cloud private native 전문가 양성과정 교재

# 3.Building Docker Image

## Dockerfile

```
# docker run -it --name c1 centos
# yum -y install httpd > /dev/null 
## systemctl start httpd
## systemctl enable httpd # httpd 이미지는 CentoOS와 호환되지 않아 설치 시 오류 발생

# docker inspect centos # container boot시 명령어가 지정되어 있음 ( "Cmd": ["/bin/sh", "-c", "#(nop)", "CMD [\"/bin/bash\"]"...)
# dodker inspect httpd # ( "Cmd": ["/bin/sh", "-c", "#(nop)", "CMD [\"httpd-foreground\"]"...)
```

이 경우 직접 컨테이너를 구동하고 수정하는 방식으로는 특정 어플리케이션 지정할 수 없음  
Dockerfile을 사용해야 함

### Dockerfile 생성

```
# mkdir dftest1
# cd dftest1
# vi Dockerfile
FROM httpd:latest
MAINTAINER nueees
RUN yum -y install httpd
COPY index.html /var/www/html
CMD /usr/sbin/httpd -D FORGROUND
# docker build -t test:1.0 . # 현재 디렉토리(.)로 빌드
# docker images # test:1.0으로 추가된 것 확인
```

1.  base image (베이스 이미지)
2.  command (실행 명령)
3.  env (환경 변수)
4.  run (실행 데몬)

| 명령어 | 설명 |
| :-: | :-- |
| FROM | 베이스 이미지 지정 |
| MAINTAINER | 작성자 지정 |
| RUN | 명령어 실행 |
| CMD | 데몬 실행 |
| LABEL | 라벨 지정 |
| EXPOSE | 포트 내보내기 |
| ENV | 환경변수 설정 |
| ADD | 파일 추가 |
| COPY | 파일 복사 |
| VOLUME | 볼륨 마운트 |
| ENTRYPOINT | 데몬실행 |
| USER | 사용자 설정 |
| WORKDIR | 작업 디렉토리 설정 |
| ONBUILD | build 후 실행 명령 |

---

## Docker image build

```
# docker build -help
# cat /root/file/Dockerfile
# docker build -t base:1.0 /root/file/
# docker images
```

### docker layer 구조

```
# docker build -t base:1.0 /root/file # 실행시 레이어 구조가 하나씩 나옴
```

1.  setp 1/5 : FROM centos
2.  step 2/5 : MAINTAINER nueees
3.  step 3/5 RUN yum -y install httpd
4.  step 4/5 COPY index.html /var/www/html
5.  step 5/5 CMD /usr/sbin/httpd -D FORGROUND

---

# 4.Docker Image Registry

## Docker image registry?

## Registry - Docker Image Registry

도커 허브에 공개되어 있는 registry 공식 이미지

```
# docker search registry
# docker pull registry:2.0
# docker images
# docker run -d -p 5000:5000 --name regTest registry:2.0

# docker tag httpd localhost:5000/regTest
# docker push localhost:5000/regTest # upload
# docker rmi localhost:5000/regTest
# docker pull localhost:5000/regTest # download
# docker images
```

## Harbor - Docker Image Registry

one of private registries  
web 기반, 다양한 기능 제공

### docker-compose 설치

```
# curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
# chmod +x /usr/local/bin/docker-compose
# docker-compose --version
```

### Harbor 설치 파일 download

```
# wget "https://github.com/goharbor/harbor/releases/download/v2.3.3/harbor-offline-installer-v2.3.3.tgz"
# tar xzvf harbor-offline-installer-version.tgz
```

HTTPS 구성 후

### Configuration File(YML File) 수정

```
# vi harbor.yml
5  hostname: docker.nueees.co.kr
13 https: 
15 port: 443
17 cerificate: 
18 private_key: 
```

### Harbor 설치

```
# systemctl restart docker
# ./install.sh
```

### Harbor 사용

```
# docker login -u admin -p Harbor12345 192.168.56.101
# docker tag centos:latest 192.168.56.101/library/docker:centos
# docker push 192.168.56.101/library/docker:centos
# docker rmi 192.168.56.101/library/docker:centos
# docker pull 192.168.56.101/library/docker:centos
```

웹에서 Harbor IP 접속하여 대시보드 확인

---
