---
layout: post
title: "[OS] 인터럽트"
categories: os
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 인터럽트

- CPU 가 프로그램을 실행하고 있을 때, 입출력 HW 장치 등에 예외사항이 발생하여 처리가 필요한 경우, CPU 에게 이를 알려 처리하는 것
- 인터럽트를 당한 시점의 레지스터와 PC 를 저장하고 CPU 의 제어를 ISR(Interrupt Service Routine) 에 넘김

### 인터럽트 종류

- 하드웨어 인터럽트: 키보드, 마우스 등 HW 가 발생시킨 인터럽트
- 소프트웨어 인터럽트: 프로그램 오류로 인한 Exception 이나 사용자 시스템 콜에 의해 발생한 인터럽트

### 인터럽트 처리 과정

1. 현재 실행 중인 명령의 메모리 주소를 포함한 부가 정보를 저장(PCB 에 저장)
2. 인터럽트 벡터(인터럽트 종류마다 처리할 코드가 위치한 부분)를 따라가서 ISR 를 처리
3. PCB 를 통해 인터럽트를 당하기 직전으로 복원하여 중단된 일을 계속 수행

## 참고

- <https://zangzangs.tistory.com/106>