---
layout: post
title: "[Web] HAproxy, Keepalived"
categories: web
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## HAProxy

- L4, L7 기반 SW 로드밸런서
- 서비스 이중화(HA)에 주로 사용
- reverse proxy 형태로 동작
- 다양한 밸런스 옵션을 제공하며 L7 헬스체크 기능도 있음

![haproxy](/assets/img/haproxy.png)

### 옵션 설정

[링크](https://www.haproxy.com/blog/the-four-essential-sections-of-an-haproxy-configuration/) 참고하여 `/etc/haproxy/haproxy.cfg` 적절히 수정

## Keepalived

- L4 기반 HA 구성
- VRRP 프로토콜을 이용
- Virtual IP 필요
- 각 서버들은 Master, Standby 로 역할이 나뉘며, Master는 VIP 사용
- Master 에 문제가 생기면 Standby 서버로 전환되고 VIP 를 넘겨 받음

![keepalived](/assets/img/keepalived.png)

## HAProxy with keepalived

- keepalived 를 통해 HAProxy 를 Active-Standby 로 구성

![haproxy with keepalived](/assets/img/haproxy-keepalived.png)

## 참고

- <https://leffept.tistory.com/309>
- <http://dveamer.github.io/architecture/HAProxyAndKeepalived.html>