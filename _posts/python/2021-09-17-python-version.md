---
layout: post
title: "[Python] 버전 별 새로운 기능"
categories: python
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Python 3.6

### Formatted string literals (PEP 498)

f-string 문법 추가

```python
>>> name = "Fred"
>>> f"He said his name is {name}"
'He said his name is Fred'
```

### Underscores in Numeric Literals (PEP 515)

긴 숫자 리터럴에 언더스코어를 넣어서 가독성을 높임

```python
>>> 1_000_000_000_000_000
1000000000000000
```

## Python 3.7

### `asyncio` 모듈 `run()` 메서드

```python
loop = asyncio.get_event_loop()
loop.run_until_complete(do_async())
loop.close()
```

한 줄로 간단히 비동기 함수 호출

```python
asyncio.run(do_async())
```

### `breakpoint()` 함수 (PEP 553)

- `breakpoint()`는 먼저 `pdb` 를 임포터하고 `pub.set_trace()` 를 호출함
- `breakpoint()` 를 만나면 실행이 중단되고 pdb 디버깅 세션에 진입

```python
def divide(e, f):
    breakpoint()
    return f / e
```

## Python 3.8

### Walrus Operator 바다코끼리 (PEP 572)

표현식에 이름을 부여하고 재사용 가능

3.7 이하에서는

```python
a = [1, 2, 3, 4]
n = len(a)
if n > 5:
    print(f"List is too long {n} elements")
```

3.8 부터는 아래와 같이 n을 조건문 안에서 선언하고 값을 대입할 수 있음

```python
a = [1, 2, 3, 4]
if (n := len(a)) > 5:
    print(f"List is too long {n} elements")
```
