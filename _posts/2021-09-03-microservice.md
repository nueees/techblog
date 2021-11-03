---
toc: false
layout: post
description: 8장-9
categories: [kubernetes]
title: Microservice Architecture
---
출처\_Building Cloud private native 전문가 양성과정 교재 + 추가 MSA 

# 8. 클라우드 서비스 소개

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



---
# 9. Microservice

## 9.1 Microservice 이해 및 구축 방법론
서비스를 비즈니스 경계에 맞게 세분화 하고, 서비스 간 통신은 네트워크 호출을 통해 진행하여 확장 가능하고 회복적이며 유연한 어플리케이션을 구성하는 것  

### Microservice 특징  
기존의 Monolithic 방식은 변화 대응이 어렵고, 새로운 기능 추가 및 업데이트가 어려움  
- 서비스마다 필요한 기술 적용 (기술 이기종성)
- 장애 격리가 쉽고 신뢰도가 높음  
- resource 낭비 줄임 (부분 scale out)  
- deploy/rollback 시 발생하는 risk 감소 (Blue-Green deploy로 점진적 변경)
  
__※ 시스템이 복잡하지 않을 경우, MSA 방식에서 요구되는 요소들이 오히려 생산성을 떨어뜨림__  
소스코드를 여러 라이브러리로 나누어서 유틸 생성하고 재사용
라이브러리를 통해 Java에 Module 컨셉을 부여해서 여러 버전의 모듈관리 (OSGI/Erlang)


### Microservice 구성 전략  

- 비즈니스 개념에 따른 모델링  
도메인 경계 정의, 기술보단 비즈니스 경계로, 서비스는 두고 DB 스키마부터 단계적 분리  
- 공통 자동화 문화 적용  
테스트 자동화 (CI[^1]/CD[^2]) 및 Custom Image 환경 구축  


#### Blue-Green 배포
![blue-green-deployment](https://www.redhat.com/cms/managed-files/blue-green-deployment-model.gif)
Blue: 이전 버전
Green: 신규 버전

#### canaria 배포
![canary-deployment](https://user-images.githubusercontent.com/83441376/139980171-e44febeb-1bee-4047-804d-79f8ebe2fd9e.png)

[배포관련 참고](https://harness.io/blog/blue-green-canary-deployment-strategies)


---
## 9.2 Spring Cloud를 활용한 Microservices 개발

### Spring Cloud
Spring Cloud는 Microservce Architecture 환경에서의 개발과 배포, 운영을 쉽게 구성할 수있도록 지원하는 Spring Boot 기반의 Framework  
Cloud Native한 패턴과 메커니즘을 제공   
많은 서비스들이 Java 기반의 Spring Boot로 개발되기에, Java 기반 MSA를 구축하고자 할 시 최적의 선택  

### Spring Boot
기존 Spring 이 가지고 있던 문제들을 해결하여, 보다 빠르게 상용 Spring Application을 개발하고자 시작  
Conventioin over Configuration (설정보다 관례)  

#### Annotaion
Annotation(@)은 JDK 1.5버전부터 지원되는 기능  
마치 코드의 메타데이터 같은 역할을 하여, Compile, Runtime 과정에서 코드를 어떻게 처리할 것인지 나타냄  
Annotation을 통해 Spring Boot가 지향하는 Convention over Configuration을 가능하게 함  



---
## 9.3 Kubernetes를 활용한 Microservice 구현






[^1]: 자동화된 빌드 및 테스트가 수행된 후, 개발자가 코드 변경을 repository에 정기적으로 병합하는 개발 방식

[^2]: 코드 변경 사항을 repository에서 고객이 사용 가능한 production 환경까지 자동으로 release하는 개발 방식



