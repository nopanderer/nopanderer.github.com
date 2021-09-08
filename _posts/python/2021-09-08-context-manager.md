---
layout: post
title: "[Python] 컨텍스트 매니저"
categories: python
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 컨텍스트 매니저

- 원하는 타이밍에 정확하게 리소스를 할당하고 제공하는 역할
- `with` 문이 가장 빈번하게 사용됨

```python
with open('some_file', 'w') as opened_file:
	opened_file.write('Hola')
```

는 다음과 같은 역할

```python
file = open('some_file', 'w')
try:
	file.write('Hola')
finally:
	file.close()
```

## 참고

- <https://ddanggle.gitbooks.io/interpy-kr/content/ch24-context-manager.html>