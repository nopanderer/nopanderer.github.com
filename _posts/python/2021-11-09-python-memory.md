---
layout: post
title: "[Python] 메모리 관리"
categories: python
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 파이썬의 메모리 관리

- 파이썬은 레퍼런스 카운트와 GC를 통해 메모리를 관리함

### 레퍼런스 카운트(Reference Count)

- 파이썬의 모든 객체는 카운트를 포함하고 각 객체가 참조될 때 증가하고, 참조가 삭제될 때 감소함
- 레퍼런스 카운터가 0이 되면 메모리 할당이 해제됨
- `sys` 라이브러리의 `getrefcount()`를 통해 객체의 카운트 수를 알 수 있음

```python
import sys

class RefExam():
  def __init__(self):
    print('create object')

a = RefExam()
print(f'count {sys.getrefcount(a)}')
b = a
print(f'count {sys.getrefcount(a)}')
c = a
print(f'count {sys.getrefcount(a)}')
c = 0
print(f'count {sys.getrefcount(a)}')
b = 0
print(f'count {sys.getrefcount(a)}')

"""
count 2 (getrefcount 파라미터로 임시 참조가 돼서 2가 출력)
count 3
count 4
count 3
count 2
"""
```

#### 레퍼런스 카운트 약점 - 순환 참조

- 객체가 자기 자신을 참조하는 것을 순환 참조라고 함
- 순환 참조가 발생하면 메모리 누수가 발생할 수 있음

```python
class RefExam():
  def __init__(self):
    print('create object')
  def __del__(self):
    print(f'destroy {id(self)}')


a = RefExam()
a = 0
print('end .....')

"""
create object
destroy 3112733520336
end .....
"""
```

위 예제는 순환 참조가 없는 일반적인 상황으로, 레퍼런스 카운트가 0이 되어 메모리가 해제됨

```python
class RefExam():
  def __init__(self):
    print('create object')
    self.me = self
  def __del__(self):
    print(f'destroy {id(self)}')

a = RefExam()
a = 0
print('end .....')

"""
OUT PUT:
create object
end .....
destroy 2110595412432
"""
```

- `self.me = self` 에서 순환 참조가 발생함
- `a = 0`에서 메모리 삭제가 돼야 할 것 같지만, 순환 참조로 인해 메모리 누수가 발생

## 가비지 콜렉션(Automatic Garbage Collection)

- 레퍼런스 카운트도 가비지 콜렉션(GC)이라고 부르지만, 순환 참조 이슈를 해결하기 위해 구현된 가비지 콜렉션을 Automatic Garbage Collection 이라고 부름
- Generational Hypothesis 라는 가설을 기반으로 작동
  - 대부분의 객체는 생성되고 오래 살아남지 못하고 곧바로 버려짐
  - 젊은 객체가 오래된 객체를 참조하는 상황을 드묾

### 세대 관리

- 객체를 3개의 세대로 구분하여 관리함
- 각 세대별로 임계값(threshold)를 두고, 임계값을 초과하면 가비지 컬렉션이 수행됨
- `collect()` 메소드를 통해 특정 세대에서 도달할 수 있는 객체와 도달할 수 없는 객체를 구분하고, 도달할 수 없는 객체(순환 참조)를 메모리에서 해제함. 도달 가능한 객체는 상위 세대로 넘김
- 순환 참조는 컨테이너 객체(e.g. `tuple`, `list`, `set`, `dict`, `class`)에 의해서만 발생할 수 있으므로, 모든 컨테이너 객체를 추적하여 순환 참조를 탐지함

## 참고

- <https://dc7303.github.io/python/2019/08/06/python-memory/>
- <https://blog.winterjung.dev/2018/02/18/python-gc>
