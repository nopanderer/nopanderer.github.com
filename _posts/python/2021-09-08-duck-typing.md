---
layout: post
title: "[Python] 덕 타이핑"
categories: python
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 덕 타이핑 (Duck Typing)

- 오리처럼 걷고 오리처럼 꽥꽥거리면 그것은 틀림없는 오리다.
- 파이썬과 같은 동적타입 언어에서, 본질적으로 다른 클래스라도 객체의 정합성은 객체의 실제 유형이 아니라 특정 메서드와 속성의 존재에 의해 결정된다는 것

```python
class Parrot:
    def fly(self):
        print("Parrot flying")

class Airplane:
    def fly(self):
        print("Airplane flying")

class Whale:
    def swim(self):
        print("Whale swimming")

def lift_off(entity):
    entity.fly()

parrot = Parrot()
airplane = Airplane()
whale = Whale()

lift_off(parrot) # prints `Parrot flying`
lift_off(airplane) # prints `Airplane flying`
lift_off(whale) # Throws the error `'Whale' object has no attribute 'fly'`
```

- `Parrot` 클래스와 `Airplane` 클래스는 서로 상속/형제 관계에 있지 않음
- 하지만 `fly()` 라고 하는 공통의 메서드를 가지고 있기 때문에 `lift_off` 메서드에서 정상적으로 실행됨

## 참고

- <https://wikidocs.net/16076>