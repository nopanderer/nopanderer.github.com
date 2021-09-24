---
layout: post
title: "[네트워크] URL 입력 시 일어나는 일"
categories: network
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 1. URL 파싱

- 브라우저는 입력받은 URL을 파싱하여 프로토콜, URL, 포트를 확인
- `https://www.naver.com` 에서 프로토콜은 `https`, URL은 `www.naver.com`, 포트는 `443`

## 2. HSTS 목록 조회

- HSTS (HTTP Strict Transport Security) 는 HTTPS 만 연결만 허용하는 기능
  - HTTP 요청에 대해서 응답 헤더에 `Strict Transport Security` 라는 필드를 포함
  - 브라우저는 이 URL을 HSTS 캐시에 저장
- HSTS 목록에 해당 URL 이 있다면, 명시적으로 HTTP 로 요청해도 브라우저가 HTTPS 로 요청함

## 3. URL 을 IP 주소로 변환

- 로컬 hosts 파일과 브라우저 캐시에 해당 URL이 존재하는지 확인
- 없다면 DNS 서버에 요청하여 해당 URL을 IP 주소로 변환

## 4. 라우터를 통해 목적지 서버의 게이트웨이까지 이동

- IP 주소를 가지고 라우터를 통해 목적지 게이트웨이까지 이동할 수 있음

## 5. ARP 를 통해 IP 주소를 MAC 주소로 변환

- 실질적인 통신을 위해서 논리 주소인 IP 주소를 물리 주소인 MAC 주소로 변환
- ARP(Address Resolution Protocol) 를 브로드캐스팅하여 해당 IP 주소를 가진 MAC 주소 확인

## 6. 대상 서버와 TCP 소켓 연결

- HTTPS 경우에는 tcp handshake + tls handshake 를 통해 연결을 맺음

![TCP TLS Handshake](/assets/img/tcp-tls-handshake.png)

## 7. HTTP(S) 로 요청

- TCP 연결 후, `www.naver.com` 페이지를 달라고 서버에 HTTP(S) 요청
- 서버는 이 요청에 대한 응답을 생성하여 브라우저에게 전달

## 8. 브라우저에서 응답 해석

- 브라우저는 서버에서 응답한 내용(html, css, jpg 등)을 해석하여 렌더링

## 참고

- <https://deveric.tistory.com/97>