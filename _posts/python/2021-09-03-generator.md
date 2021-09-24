---
layout: post
title: "[Python] Iterable, Iterator, Generator"
categories: python
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Abstract Base Class (ABC)

- 파이썬에서는 모든 것이 클래스고, 자료구조 또한 클래스를 통해 구현됨
- `collections.abc` 모듈에 자료구조들의 토대가 되는 추상 자료 구조가 클래스도 관리되고 있음

```python
from collections.abc import Container, Collection, Sequence

print(Container.__abstractmethods__)
print(Collection.__abstractmethods__)
print(Sequence.__abstractmethods__)

frozenset({'__contains__'})
frozenset({'__len__', '__contains__', '__iter__'})
frozenset({'__len__', '__getitem__'})
```

- 자식 클래스는 이처럼 추상 클래스의 각 메소드를 온전히 구현해야 함
- `list`, `tuple`, `set`, `dict` 모두 `Collection`을 상속받음
- 여기서 다루는 `Iterable`, `Iterator`, `Generator` 모두 ABC에 정의되어 있는 추상 클래스임

## Iterable, Iterator

### Iterable

- `collections.abc`에 정의되어 있는 추상 클래스
- 순회가능한 모든 객체. for 문에서 `in` 키워드에 올 수 있는 것
    - list, tuple, set, dict, range, 문자열, 파일 등
- 직접 iterable 를 상속받기 위해서는 `__iter__` 추상 메소드를 구현해야 함
    - `__iter__` 메소드는 호출될 때마다 새로운 iterator 를 반환해야 함

### Iterator

- Iterable 를 상속받음
- 상태를 유지하며 반환할 수 있는 마지막 값까지 원소를 필요할 때마다 하나씩 반환
- Iterable 과 비슷하지만, 상태를 갖는다는 것이 중요함
- 여기서 '상태'란 각 iterator가 순회하고 있는 위치
- iterable 에 iter 메소드를 사용할 때마다 새로운 iterator 가 반환됨. 각 iterator는 서로 다른 상태를 유지함

```python
l = [1,2,3]
assert iter(l) != iter(l)
```

- 어떤 클래스가 iterator 이기 위한 조건
    - `__iter__` 를 구현하되, 자기 자신을 반환
    - `__next__`를 구현해서 iterator 를 인자로 주었을 때 다음에 반환할 값을 정의
    - iterator가 더이상 반환할 값이 없는 경우 StopIteration 예외를 일으킴

### 파이썬에서 `for` 문 동작 과정

```python
for n in 1,2,3,4,5:
    print(n)
```

- for 뒤에 오는 튜플은 iterable
- 튜플 1,2,3,4,5 에 iter 메소드를 통해 iterator 를 얻음
- iterator 에 next 함수를 써서 하나씩 값을 얻음
- next 결과로 StopIteration 예외 처리가 반환되면 반복문 종료

## Generator

- iterator 를 상속받음(상속관계: generator -> iterator -> iterable)
- iterator 기능을 만들되, 생성 문법을 단순화한 것
- 이것 역시 `collections.abc`에 정의된 추상 클래스

### 생성 방법 1. yield

```python
from random import randint

def random_number_generator(n):
    count = 0
    while count < n:
        yield randint(1, 100):
        count += 1
```

다음과 같은 특징이 있음

- 클래스가 아닌 함수로 정의
- iterable, iterator 를 분리하지 않고 한 요소로 담을 수 있음
- 호출될 때마다 하나씩 반환할 때 `yield` 키워드를 사용
- `next` 메소드를 통해 다음 값 반환

### 생성 방법 2. generator comprehension

- list comprehension 과 비슷한 문법으로 `yield` 키워드 안쓰고 generator 를 만들 수 있음

```python
from random import randint

g = (randint(1, 100) for _ in range(5))

for n in g:
    print(n)

96
34
71
13
33
```

## Generator(Iterator) 를 쓰는 이유?

- lazy loading 으로 인한 메모리 안정성
- generator(iterator) 는 지정한 규칙대로 값을 반환할 규칙과 현재 어디까지 반환했는지 정보만 있고, 값 전체를 메모리에 할당하지 않음

```python
big_list = [i for i in range(1, 1000000000000000000)]
```
리스트는 모든 값을 메모리에 다 올려두기 때문에, 위 문장은 실행이 안됨

```python
big_gen = (i for i in range(1, 1000000000000000000))
```
반면 제너레이터는 문제없이 잘 생성됨. 생성 규칙과 다음에 반환할 값 정보만 메모리에 올리기 때문.

- 따라서 매우 큰 데이터를 다룰 때 generator 를 쓰면 메모리를 절약하고 안전성을 높일 수 있음

## 참고

- [https://shoark7.github.io/programming/python/iterable-iterator-generator-in-python](https://shoark7.github.io/programming/python/iterable-iterator-generator-in-python)