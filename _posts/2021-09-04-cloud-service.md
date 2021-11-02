---
toc: false
layout: post
description: 8장
categories: [kubernetes]
title: Cloud Service
---
출처\_Building Cloud private native 전문가 양성과정 교재

# 8.클라우드 서비스 소개

## 8.1. 클라우드 서비스 개요

-   네트워크를 이용해서 사용자들이 원하는 방식으로 서비스를 제공
-   확장성
-   사용 모델
-   분산
-   개인 시스템의 성능에 구애받지 않음

### 장점

-   초기 구성 비용 절감
-   초기 구성 시간 절약
-   확장성이

### 단점

-   오랜 기간 사용할 경우 비용 부담 증가

## 8.2. 클라우드 서비스 분류

### SaaS (Software as a Service): 서비스로서 소프트웨어 애플리케이션 서비스를 제공

보통 IaaS, PaaS위에 올라가고, 중앙에서 호스팅되고 있는 소프트웨어를 웹 브라우저 같은 애플리케이션을 통해 사용  
ex) Google Docs

### PaaS (Platform as a Service): 서비스로서 플랫폼 소프트웨어를 제공

SaaS의 개념을 개발 플랫폼으로 확장한 방식  
플랫폼(OS)를 웹에서 쉽게 빌려 사용  
확장성과 경제적 이유로 On-Premise환경을 Cloud로 확장  
ex) Google APP Engine, OpenShift

### IaaS (Infrastructure as a service):서비스로서 인프라 자원 제공

Server, Storage, Network를 가상으로 만들어 사용자가 필요한 자원 사용  
관리와 책임이 클라우드 소비자에게 존재

ex) 대부분 퍼블릭 클라우드 서비스 (AWS EC2, S3)

## 8.3. 클라우드 서비스 종류

### Private Cloud

자체적으로 데이터센터 안에 클라우드 환경 구축

-   자산 스스로 보유, 구축
-   기존 IT 인프라 자원 활용 가능
-   소규모로 구축할 때 비용이 높음
-   보안 서비스를 자체적으로 구축해야 함

### Public Cloud

비용을 지불하고 서비스 제공 업체가 구축한 Server, Storage, Network 등 IT Infra 사용

-   공용 클라우드는 가입 형태의 서비스
-   대규모 서비스로 구축 시 비용 절감
-   서비스 제공자가 구축한 보안 서비스 안에서 운용

### Hybrid Cloud

공용 + 사설 클라우드의 장점만 선택해서 사용

-   필요에 의해 데이터나 컴퓨팅 자원의 위치 조절
-   데이터의 중요도와 비즈니스 핵심 업무 여부에 따라 선택 가능
