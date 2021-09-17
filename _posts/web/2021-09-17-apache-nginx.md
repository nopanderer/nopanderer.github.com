---
layout: post
title: "[Web] Apache vs. Nginx"
categories: web
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Apache

- MPM (Multi Processing Module) 방식으로 클라이언트 요청을 처리
- 프로세스/스레드 기반 구조로 동작
	- ServerSocket 으로 요청 A가 들어오면 스레드 할당
	- 스레드는 해당 소켓을 가지고 I/O 작업 수행
	- ServerSocket 으로 새로운 요청 B가 들어오면 새로운 스레드 할당하고 컨텍스트 스위치 후 I/O 작업 수행

![pcb](/assets/img/apache.png)

### Prefork MPM

![pcb](/assets/img/prefork-mpm.png)

- 프로세스를 복제하여 요청 처리
- 프로세스 당 하나의 스레드
- 프로세스 복제 방식이기 때문에 메모리 비용이 큼

### Worker MPM

![pcb](/assets/img/worker-mpm.png)

- 프로세스 당 여러 개의 스레드를 생성하여 요청 처리
- Prefork 방식보다 메모리 소모가 적고 통신량이 많을 때 유리

### Event MPM

- Worker MPM 방식에서 `Keep Alive` 한 요청을 처리하는 스레드를 따로 둠

## Nginx

- 한개 또는 고정된 프로세스만 생성하고, 프로세스 내부에서 비동기 방식으로 요청 처리
- 프로세스/스레드 생성 비용이 적음

![pcb](/assets/img/nginx.png)

- Reactor Pattern
	- Reactor 는 event 가 들어오면 알맞은 handler 로 dispatch
	- Handler 는 dispatch 된 event 를 받아서 처리

## Apache, Nginx 성능 비교

- Nginx 가 전반적으로 우세한 성능을 보인다고 하나 그 편차가 크지 않다고 함(좀더 조사 필요)

## 참고

- <https://cornswrold.tistory.com/429>
- <https://stackoverflow.com/questions/27856231/why-is-the-apache-event-mpm-performing-poorly>