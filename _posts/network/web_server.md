# Web Server와 WAS

## Web Server

HTTP 요청을 받고 HTML, CSS, JS, Image와 같은 **정적인 정보**를 반환하는 역할을 한다. 대표적인 웹서버는 Apache, Nginx 등이 있다.(proxy 글 참조)

## Web Application Server (WAS)

<img src="/images/was.PNG" width=500>

웹 서버와 웹앱 사이의 **동적인 정보**를 생성하는 역할을 담당한다. 이를 위한 인터페이스로 CGI (Common Gateway Interface)라는 것을 사용하는데, Web Server + CGI 형태를 WAS 라고 한다. JVM 계열에서는 Tomcat, JBoss 등이 대표적인 WAS 이다. 파이썬에서는 별도의 WAS가 존재하지 않는데, 파이썬에는 파이썬만의 게이트웨이 인터페이스가 존재한다.

## WSGI (Web Server Gateway Interface)

<img src="/images/wsgi.PNG" width=700>

파이썬에서 웹 어플리케이션 (파이썬 스크립트) 가 웹 서버와 통신하기 위한 명세이다. 일종의 프로토콜 혹은 인터페이스 개념으로 보면 된다. WSGI 요청을 처리하려면 서버에서 환경정보와 콜백함수를 앱에 제공해야 한다. 앱은 그 요청을 처리하고 콜백함수를 통해 서버에 응답한다. Flask, Django와 같은 프레임워크는 이미 WSGI 표준을 따르고 있기 때문에 바로 웹 서버에 연결해서 사용할 수도 있다.

## WSGI Middleware

WSGI의 실제 구현? 이라고 보면 된다. 서버의 관점에서는 앱으로, 앱의 관점에서는 서버로 행동한다. 미들웨어에는 uWSGI, gunicorn 등이 있다. 앱 관점에서는 이 미들웨어를 통해 앱이 실행되므로 앱을 실행시켜주는 어플리케이션 컨테이너(Application Container)라고도 한다.(자바로 치면 서블릿인듯?)


몇몇 글에 따르면 uWSGI는 gunicorn 등에 비해 무겁다(cpu, ram 등 자원소모가 크다)는 이야기가 많다. uWSGI에서 gunicorn으로 대체하고 response time이 절반으로 줄었다고 하는 이야기가 있는만큼 uWSGI를 쓰는 건 지양해야 할 것으로 보인다.


## ASGI

WSGI는 동기 함수 처리만을 지원하여 여러 작업을 동시에 처리하는 것에 한계가 있다. 예를 들면, 웹 소켓 등을 활용한 실시간 채팅 서비스는 WSGI로 구현이 어려울 수 있는 것이다. 물론 비동기 큐와 같은 Celery 를 잘 활용한다면 대용량 트래픽 처리를 요구하는 서비스 구현이 불가능한 것은 아니지만 어려움이 있다.


따라서 최근에 Django 3.0이나 FastAPI 등의 프레임워크에서는 ASGI 인터페이스를 적용하였다. 대표적인 ASGI 미들웨어로 uvicorn이 있다.

<img src="/images/asgi.PNG" width=700>

아키텍쳐로 보았을 땐 WSGI와 차이가 없어보이지만, asgi는 기본적으로 요청을 비동기로 처리한다는 점이 큰 차이점이다. 따라서 WSGI에서는 지원하지 않는 웹 소켓 프로토콜과 HTTP 2.0을 지원한다.


## Framework

서버 어플리케이션을 만들기 위해 사용하는 최상단 웹 프레임워크이다. micro 프레임워크로 대표되는 Flask와 Full-stack 프레임워크로 대표되는 Django, 최근에 유명세를 얻고 있는 FastAPI 등이 있다.


## WAS가 Web Server의 기능도 모두 수행하면 되지 않을까?

1. 기능을 분리하여 서버 부하를 방지 (정적인 컨텐츠를 웹 서버에서 동적인 컨텐츠는 WAS에서)

2. 물리적으로 분리하여 보안 강화 (SSL 처리 등을 웹 서버에서)

3. 여러 대의 WAS를 웹 서버에 연결 가능 (로드 밸런싱, Fail  over 등)


즉, 자원 이용의 효율성 및 장애 극복, 배포 및 유지 보수 편의성을 위해 Web Server와 WAS를 분리한다.


## 요약

<img src="/images/web_server.PNG" width=500>

1. HTTP 요청이 들어오면

2. 웹 서버가 해당 요청을 받고, 서버사이드 처리가 필요없으면 리스폰스를 리턴 (static한 웹 서버)

3. 서버사이드 처리가 필요하면 wsgi 미들웨어를 통해 파이썬 어플리케이션으로 요청 전달

4. 파이썬 어플리케이션이 요청을 받아 처리 후, wsgi 미들웨어 - 웹서버를 통해 리스폰스 리턴