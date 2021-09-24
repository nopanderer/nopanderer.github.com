---
layout: post
title: "[Python] 언더스코어"
categories: python
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 언더스코어

파이썬에서 사용되는 언더스코어(`_`)는 다양하게 사용됨

### 단일 밑줄 접두사: `_var`

- 해당 변수/메서드는 private 하게 쓰일 것이라는 의도
- 접근은 가능함. 일종의 컨벤션

```python
# underscore.py
var1 = "hello"
_var2 = "world"
```

```python
from underscore import *
print(dir(underscore)) # _var2 는 출력되지 않음
```

### 단일 밑줄 접미사: `var_`

`dict`, `class` 등 키워드를 변수명으로 사용하고 싶을 때

```python
class_ = "123"
```

### 이중 밑줄 접두사: `__var`

- 네임 맹글링(name mangling)으로 인터프리터가 해당 속성 이름을 변경(mangling)
    - 1) 클래스 속성값을 외부에서 접근할 수 없도록
    - 2) 서브클래스에서 부모클래스의 속성을 오버라이딩하지 못하게 하기 위해 사용

```python
class Practice:
    def __init__(self):
        self.a = 11
        self._b = 12
        self.__c == 42

class ExtendedPractice(Practice):
    def __init__(self):
        super().__init__()
        self.a = 22
        self._b = 22
        self.__c == 52
t2 = ExtendedPractice() # AttributeError
```

### 이중 밑줄 접두사와 접미사: `__var__`

- 매직메서드로 특수 용도로 예약되어 있음
- `__new__`, `__init__`, `__repr__` 등

### 단일 밑줄 문자: `_`

```python
# 2는 무시
a, _, c = 1, 2, 3

# 루프만 돌리고 싶음
for _ in range(10):
    print("Hello World")

# 큰 숫자 자릿수 구분
large_num = 1_000_000_000
```

## 참고

- <https://blog.naver.com/PostView.naver?isHttpsRedirect=true&blogId=reisei11&logNo=221749496623>
- <https://tibetsandfox.tistory.com/21>