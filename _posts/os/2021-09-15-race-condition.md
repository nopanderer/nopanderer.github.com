---
layout: post
title: "[OS] Race Condition"
categories: os
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Race Condition

- 여러 개의 프로세스/스레드가 공통의 자원을 놓고 경쟁하는 상황
- 두 개 이상의 프로세스/스레드가 공통 자원을 공통적으로(concurrently) 읽거나 쓰는 동작을 할 때, 접근 순서에 따라 실행 결과가 같지 않고 달라지는 상황

### Mutual Exclusion

- race condition 을 막기 위해서는 두 개 이상의 프로세스/스레드가 공용 데이터에 동시에 접근하는 것을 막아야 함
- Mutex와 Semaphore 두 가지 방법이 있음

#### Mutex

- critical section (공유 데이터가 엑세스 되는 코드부) 을 `lock`을 이용해서 여러 개의 프로세스/스레드가 접근하는 것을 막음
- 단 하나의 프로세스/스레드만 CS에 접근 가능

```python
lock.acquire()
# critical section start
count += 1
# critical section end
lock.release()
```

#### Semaphore

- `wait()` 함수와 `signal()` 함수를 이용하여 동시에 접근할 수 있는 프로세스/스레드 수를 제어
- block and wakeup 방식으로 구현
	- S: 세마포어 값으로 초기 값만큼 여러 프로세스/스레드가 CS에 동시 접근 가능
	- P(S): CS에 들어갈 때. 1 빼고 S가 0 미만이면 대기(block)
	- V(S): CS에서 나올 때. 1 증가시키고 S가 0 이하이면 wakeup

```
P(S)
// critical section
V(S)
```

### Deadlock

- mutual exclusion 으로 인해 생기는 문제
- 두 개 이상의 프로세스/스레드가 서로 상대방의 작업이 끝나기를 기다리고 다음 단계로 진행하지 못하는 상태
- 식사하는 철학자 문제

#### deadlock 발생 조건

아래 조건 모두 충족

1. mutual exclusion
2. 점유 대기
3. 비선점
4. 순환 대기

#### deadlock 해결 방법

- 예방: 4가지 조건 중 하나라도 제거
- 회피: 자원 요청에 대한 정보를 이용해서 동적으로 미리 deadlock safe 한지 파악 후, 자원 할당(은행원 알고리즘)

### Starvation

- 특정 프로세스/스레드의 우선순위가 낮아서 원하는 자원을 계속 못 받는 상태

#### Starvation 해결 방법

- 프로세스 우선 순위를 수시로 변경
- 오래 기다린 프로세스의 우선순위를 높임
- 우선순위가 아닌 FIFO 기반 요청 큐를 사용

## 참고

- <https://worthpreading.tistory.com/90>
- <https://jwdeveloper.tistory.com/221>
- <https://libertegrace.tistory.com/entry/%EC%9A%B4%EC%98%81%EC%B2%B4%EC%A0%9C-Semaphore%EC%99%80-deadlock-starvation>