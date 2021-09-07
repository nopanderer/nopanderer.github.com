---
layout: post
title: "[네트워크] HTTP 메소드 정리"
categories: network
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## HTTP 메소드 정리

### GET

리소스 획득. 서버에 존재하는 데이터를 요청

### POST

엔티티 전송. 서버에 데이터를 생성하는 것을 요청

### PUT

서버에 존재하는 데이터를 수정하거나 존재하지 않으면 생성

### PATCH

- entity의 일부만 바꿈(ex - 일부 fields)를 바꿈.
- PUT replaces an existing entity. If only a subset of data elements are provided, the rest will be replaced with empty or null.

### DELETE

파일 삭제. PUT매서드와 반대. 인증기능이 없어서 REST를 사용하는 경우에 사용.

### HEAD

GET과 같은 기능이지만 메시지 바디는 반환 안함. URI 유효성과 리소스 갱신 시간을 확인하는 목적으로 사용.

### OPTIONS

제공하고 있는 메소드의 문의. 리퀘스트 URI로 지정한 리소스가 제공하고 있는 메소드를 조사하는데 사용함.

### TRACE

경로조사. 프록시등을 중계할떄 추적위해 사용. 크로스 사이트 트레이싱(XST) 같은 공격을 일으킬 수 있으므로 거의 안쓰임.

## 참고

- <https://seunghyum.github.io/network/Network/#>
- <https://github.com/ksundong/backend-interview-question>
