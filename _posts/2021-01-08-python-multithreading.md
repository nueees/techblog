---
toc: true
layout: post
description: section8
categories: [python]
title: Python Multithreading
---

Python Intermediate Programming2

---

## 8.1. Process & Thread

### Process:  
- any program is in execution
- 독립적인 Process Control Block(PCB), Stack, Address Space 가짐
- Code, Data, Stack, Heap 독립적으로 할당
- 최소 1개의 메인스레드 보유
- 생성, 소멸, 통신(Context Switching via pipe, socket, file...)하는데 cost가 높음
- 프로세스 전환시 OS 호출 필요

### Thread:  
- segment of a process
- 부모인 프로세스의 자원 사용
- Stack만 별도 할당 나머지는 공유(Code, Data, Heap)
- 메모리 공유(변수 공유)
- 한 스레드의 결과가 다른 스레드에 영향 끼침
- 하나 스레드 block 시 다른 스레드도 blocked(디버깅 어려움, 동기화 주의)
- 스레드 전환시 OS 호출 및 kernel 중단 불필요

### Multi process:  
- 한 개의 프로그램을 여러 프로세스로 구성하여 각 프로세스가 하나의 작업(task) 처리
- 한 개의 프로세스 문제 발생은 확산 없음(프로세스 Kill)
- 캐시 체인지, context switching 비용 매우 높음(오버헤드), 복잡한 통신 방식 사용

### Multi thread:  
- 한 개의 프로그램을 여러 스레드로 구성 후 각 스레드가 하나의 작업(task) 처리
- 시스템 자원 소모 감소(효율성), 처리량 증가(Cost 감소)
- 통신 부담 감소, 디버깅 어려움, 단일 프로세스에는 효과 미약, 자원 공유 문제(교착상태), 프로세스 영향준다.
- 시스템 자원 소모 감소, 처리 비용 감소(실행 속도 향상), 쓰레드간 자원 공유(stack 영역) 
- 디버깅 어려움, 동기화 이슈 발생, 하나의 쓰레드의 오류로 전체 프로세스에 문제 발생



