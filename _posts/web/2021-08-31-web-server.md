---
layout: post
title: "[Web] 웹서버, WSGI"
categories: web
---

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 웹서버와 웹앱

### 웹서버

- HTTP 를 통해 웹 브라우저에서 요청하는 그림, css, js, html 문서 등 **정적 컨텐츠**를 클라이언트에게 제공
- WAS 로 가는 부하를 줄이기 위해 사용
- 대표적인 예로 apache http server, nginx 등이 있음

### 웹앱

동적인 요청이 들어오는 경우(DB 조회 등), 비즈니스 로직을 처리하기 위해 web app 에게 요청을 위임. web app은 로직을 수행하고 결과를 web server에게 반환

## CGI (Common Gateway Interface)

- 2003년 까지 파이썬 웹 프레임워크는 CGI 로 웹 서버와 대화함
- 웹 서버가 먼저 클라이언트의 http request를 받음
- 웹 서버는 http request 의 정보를 환경 변수, 표준 입력을 통해 web app 에게 전달
- web app 은 스크립트를 실행해서 비즈 로직을 수행하고 결과를 표준 출력으로 웹 서버에게 전달
- 웹 서버는 이를 다시 클라이언트에게 전달
- 매번 스크립트를 실행하여 메모리 적재하는 과정이 번거롭고 시간도 소요되어 wsgi가 등장

## WSGI (Web Server Gateway Interface)

- 웹 서버와 웹 어플리케이션 사이 인터페이스를 위한 파이썬 프레임워크
- callable object 를 통해 웹 서버가 요청한 정보를 web app 에게 전달
- 웹 서버는 callable object 를 통해 2가지 정보를 전달
  - http request 에 대한 정보
  - callback 함수
- 웹 앱은 http request 에 대한 정보를 가지고 biz logic 을 수행한 후에, callback function 을 통해 웹 서버에게 반환
- WSGI middleware(실제 구현체) 로 uWSGI, gunicorn. 비동기로는 uvicorn
- Django, Flask 등은 이미 wsgi 표준을 따르고 있어서 바로 웹 서버에 연결할 수도 있음

## WSGI Middleware

- web application 의 실행 전과 후에 필요한 기능들(auth, routing, session, cookie 등)을 추가해주는 역할
- decorator pattern 과 비슷
- 대표적으로 uWSGI, gunicorn 이 있음. 얘네는 자체적으로 웹 서버 역할을 할 수도 있음

## ASGI

- 요청을 비동기로 처리
- WSGI 에서는 지원하지 않는 웹 소켓, HTTP 2.0 지원
- Django 3.0, FastAPI 등의 프레임워크에서 ASGI 인터페이스를 지원
- 대표적인 ASGI 미들웨어로 uvicorn 이 있음

## WAS가 웹 서버 기능도 모두 수행하면 되지 않을까

- 기능을 분리하여 서버 부하 방지 (정적인 컨텐츠는 웹 서버에서, 동적인 컨텐츠는 WAS에서)
- 물리적으로 분리하여 보안 강화 (SSL 처리 등을 웹 서버에서)
- 여러 대의 WAS 를 웹 서버에 연결 가능 (LB, failover 등)

## 참고

- [https://sgc109.github.io/2020/08/15/python-wsgi/](https://sgc109.github.io/2020/08/15/python-wsgi/)