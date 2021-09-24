---
layout: post
title: "[OS] Thread Safe"
categories: os
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Thread Safe

- Thread Safe 하다는 것은 스레드들이 race condition 을 발생시키지 않으면서 각자의 일을 수행한다는 뜻
- 좀 더 구체적으로는 멀티 스레드 프로그래밍에서 어떤 함수, 변수, 혹은 객체가 여러 스레드로부터 동시에 접근이 이루어져도 프로그램의 실행에 문제가 없음을 뜻함
	- 파이썬에서 `requests.Session()` 객체는 thread-safe 하지 않아서 스레드 별로 별도의 객체를 만들어서 써야함

## Thread Safe 를 지키는 방법

### Mutual exclusion (mutex)

- 공유되는 메모리의 데이터를 여러 스레드가 동시에 사용할 수 없도록 잠궈버림
- 일반적으로 이 방법이 자주 사용됨
- 파이썬에서는 `threading.Lock`

### Re-entrancy

- 어떤 함수가 한 스레드에 의해 실행 중일 때 스케줄링되어 다른 스레드가 그 함수를 호출하더라도 결과가 각각 올바르게 나와야 함

### Thead-local storage

- 공유 자원을 최대한 줄여 각각의 스레드에서만 접근 가능한 저장소들을 사용하여 동시 접근을 막음
- 파이썬에서는 `threading.local()` 을 통해 각 스레드에 특정 객체를 만듦

```python
import requests
import threading

thread_local = threading.local()

def get_session():
	if not hasattr(thread_local, "session"):
		thread_local.session = requests.Session()
	return thread_local.session
```

- 위 코드에서처럼 전역적으로 하나만 만들어놓으면 내부적으로 각각의 스레드마다 서로 다른 데이터에 접근하도록 처리됨

### Atomic operations

- 공유 자원 변경 시 "원자적"으로 정의된 접근 방법을 이용
- 파이썬에서 `+=` 같은 연산은 원자적이라고 보기 어려움 (`+` 후에 `=` 연산을 하기 때문)

## 참고

- <https://dgkim5360.tistory.com/entry/understanding-the-global-interpreter-lock-of-cpython>
- <https://gompangs.tistory.com/entry/OS-Thread-Safe%EB%9E%80>
