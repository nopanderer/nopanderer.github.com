---
layout: post
title: "[Python] 인터프리터"
categories: python
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 파이썬 인터프리터

- 파이썬은 인터프리터를 사용하는 언어로 알려져있으나, 파이썬 인터프리터 내부에는 컴파일러와 파이썬 가상 머신(PVM)이 있음
- 소스코드(`.py`)에 대해 컴파일러가 바이트코드(`.pyc`)로 변환하고, 이를 한줄씩 PVM이 해석하는 과정을 거침
- 파이썬 코드를 실행하다 보면 `__pycache__` 라는 디렉토리가 생기고 그 안에 `.pyc` 파일들이 생기는 것을 볼 수 있음

![python interpreter](/assets/img/interpreter.png)

### CPython

- 가장 일반적인 파이썬 인터프리터로 C로 구현됨

### PyPy

- JIT 컴파일을 도입하여 CPython 보다 성능이 좋음
- JIT (Just In Time) 컴파일은 프로그램을 실제 실행하는 시점에 기계어로 번역(dynamic translation)하는 컴파일 기법
- 인터프리터 방식으로 실행되다가 특정 시점에 바이트코드(`.pyc`) 전체를 컴파일하여 네이티브 코드로 변경하고 이후에는 해당 메서드를 더이상 인터프리팅하지 않아 실행 속도를 높임
- 네이티브 코드를 실행하는 것이 한줄씩 인터프리팅하는 것보다 빠르고, 네이티브 코드는 캐시에 보관하기 때문에 한 번 컴파일 된 코드는 빠르게 수행됨

## 참고

- <https://has3ong.github.io/programming/pypy-cpython/>
- <https://indianpythonista.wordpress.com/2018/01/04/how-python-runs/>
