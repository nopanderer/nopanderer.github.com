---
layout: post
title: "[Python] is와 =="
categories: python
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## is

변수가 같은 객체를 가리키면 `True`

### is 예시

```python
>>> a = [1,2,3]
>>> b = a
>>> c = [1,2,3]
>>> a is b
True
>>> a is c
False
```

## ==

변수가 같은 값을 가지면 `True`

### == 예시

```python
>>> a = [1,2,3]
>>> b = a
>>> c = [1,2,3]
>>> a == b
True
>>> a == c
True
```

## 참고

- <https://twpower.github.io/117-difference-between-python-is-and-double-equal>