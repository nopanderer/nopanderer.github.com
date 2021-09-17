---
layout: post
title: "[OS] Sync, Async, Blocking, NonBlocking"
categories: os
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 서두

![pcb](/assets/img/sync-async.png)

동기 처리와 비동기 처리에 대해서 대부분의 사람들은 어렴풋이라도 알고 있다. 동기 처리는 요청을 하고 응답을 받을 때까지 대기하는 것이고, 비동기 처리는 요청을 하고 응답을 받을 때까지 다른 일을 하는 것... 그래서 `동기=Blocking`, `비동기=NonBlocking` 이라고 생각하기 쉬운데, 위 표를 보면 마치 상반되는 것들이 묶여있는 것을 볼 수 있다. 대체 asynchronous blocking 은 뭘까? synchronous nonblocking 은? 오늘 이들 간에 어떤 차이점이 있는지 알아보도록 한다.

## Synchronous, Asynchronous

- Synchronous/Asynchronous 는 호출되는 함수의 작업 완료 여부를 누가 신경쓰느냐가 관심사
	- asynchronous: 호출되는 함수에게 콜백을 전달하고, 호출하는 함수는 이를 신경쓰지 않음
	- synchronous: 호출하는 함수가 호출되는 함수의 작업 완료 후 리턴을 기다리거나, 콜백을 전달하고 바로 리턴을 받더라도 작업 완료 여부를 계속 확인하면서 신경씀

## Blocking, NonBlocking

- Blocking/NonBlocking 은 호출되는 함수가 바로 리턴하느냐 마느냐가 관심사
	- Blocking: 호출된 함수가 자신의 작업을 모두 마칠 때까지 호출한 함수에게 제어권을 넘겨주지 않고 대기하게 만듦
	- NonBlocking: 호출된 함수가 호출한 함수에게 바로 제어권을 넘겨주고, 호출한 함수가 다른 일을 할 수 있도록 기회를 줌

## 헷갈리는 조합 살펴보기

### NonBlocking-Sync

- 호출되는 함수는 바로 리턴하고, 호출하는 함수는 작업 완료 여부를 계속 체크
- 폴링 방법이 대표적인 예

### Blocking-Async

- 호출되는 함수가 바로 리턴하지 않고, 호출하는 함수는 작업 완료 여부를 신경쓰지 않음
- 이거는 다시 봐도 굉장히 이상해보임
- 실제로 이 방식이 필요한 경우는 없고, nonblocking-async 를 쓰다가 본의 아니게 이 방식으로 동작한다고 함 (db 드라이버가 blocking 방식인 경우가 많다고 함)

## 참고

- <homoefficio>
