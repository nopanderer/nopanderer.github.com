---
layout: post
title: "[네트워크] HTTP 버전별 차이"
categories: network
---

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## HTTP 1.0

- 96년에 발표
- 1.0 초기에는 TCP 연결이 3-way handshake 로 이루어졌음(SYN - SYN, ACK - ACK)
	- 5개의 오브젝트를 가진 웹 페이지가 있다면 총 5번의 3-way 핸드쉐이크를 해야함
- 이를 개선하기 위해 **Persistent Connection(Keep alive)** 기술이 등장

### Persistent Connection (Keep Alive)

- 클라이언트가 서버에게 요청 시 헤더에 `connection: keep-alive` 를 추가
- 서버에서는 클라이언트의 요청을 받아 동일한 헤더를 포함하여 응답 (HTTP 응답 이후 끊지 않고 TCP 연결을 계속하겠음)

![persistent connection](/assets/img/persistent-connection.png)

## HTTP 1.1

- 99년에 발표
- 1.0 의 Persistent Connection 기능이 디폴트로 장착됨(`connection: close` 헤더를 붙이는 경우에만 응답 이후 TCP 연결을 끊음)
- 각 요청들의 응답을 순차적으로 기다리는 문제를 해결하기 위해 요청들을 한꺼번에 보내고 응답 처리를 한꺼번에 받는 **Pipelining** 기술이 등장(네트워크 레이턴시 감소)
- 클라이언트는 각 요청에 대한 응답을 기다리지 않고, 여러개의 HTTP 요청을 보냄
- 하나의 TCP 연결에서 복수개의 HTTP 요청을 담아서 보냄
- TCP 특성상 응답 순서가 보장되어야 하기 때문에, 앞의 요청을 처리하는데 오래 걸리면 뒤 요청들의 응답도 늦게 처리 됨(**HOL Blocking: Head Of Line Blocking**)

![pipelining](/assets/img/pipelining.png)

## HTTP 2.0

- 15년에 발표

### Multiplexed Streams

- 한 커넥션에서 여러개의 메시지를 주고 받을 수 있으며, 응답은 순서에 상관없이 스트림으로 주고 받음
- 스트림이 뒤섞여도 stream number 를 이용해 수신측에서 재조합함

![multiplexing](/assets/img/multiplexing.png)

### Server Push

- 클라이언트가 요청하지 않은 리소스를 서버가 마음대로 보냄
- 클라이언트는 html 만 요청하더라도, 서버가 이를 분석하여 이에 필요한 css, jpg 등의 리소스들을 보냄으로써 클라이언트의 요청을 최소화 함

![Server Push](/assets/img/server-push.png)

### Header Compression

- 헤더에서 중복되는 필드은 재전송하지 않도록 함
- HTTP 헤더 자체를 해시 테이블과 허프만 코딩을 이용해 압축

## HTTP 3.0

- UDP 기반인 QUIC 프로토콜 사용