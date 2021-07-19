---
layout: post
title: "[쿠버네티스 기초 14] Imperative vs. Declarative"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

Service 에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Imperative

- Step by Step 으로 
- how 에 가까움

예시

```
1. web server 라는 이름의 VM을 만든다
2. 거기다가 nginx 를 설치함
3. 8080포트로 설정
4. web path 설정
5. git 에서 path에 파일 받아 옴
6. nginx 실행
```

## Declarative

- 원하는 상태를 명시
- what 에 가까움
- Terraform, Ansible 이 이러한 형태

예시

VM Name: web-server
Database: nginx
Port: 8080
Path: /var/www/nginx
Code: Git Repo - X

## Imperative vs. Declarative

- 둘의 가장 큰 차이는 idempotent
- 중간에 어떤 에러 때문에 처음부터 재실행하는 경우, imperative 에서는 중복 실행 이슈와 같은 것들을 처리하기 어려움
- declarative 는 상태를 규정하는 것이기 때문에 시스템이 인텔리하게 처리함
- 특히 버전 변경과 같은 것(nginx -> nginx:2)이 declarative 방식에서 좋음

## 쿠버네티스에서의 명령형

- `run`, `create`, `expose`, `edit`, `scale`, `set`, `create -f`, `replace -f`, `delete -f` 와 같은 커맨드
- 이미 해당 오브젝트가 존재하는데 `create -f` 를 날리거나 혹은 해당 오브젝트가 없는데 `replace -f` 를 날리면 에러가 생김 (**명령형의 단점**)

## 쿠버네티스에서의 선언형

- 원하는 상태(desired state)를 yaml 파일에 정의하고 `apply -f` 커맨드로 실행
- API 는 현재 파일을 보고 어떤 점이 바뀌었는 지 체크해서 적절한 조치를 취해 줌(`create` 와 `replace`를 한번에)
- 오브젝트가 없으면 만들어주고 있는 경우 바뀐 부분만 체크해서 업데이트 해줌
- 반복해서 실행해도 항상 같은 상태가 유지됨을 보장(**idempotent**)

## kubectl apply 동작 과정

- 로컬에 있는 yaml 파일을 가지고 오브젝트를 생성. 이와 동시에 로컬 파일과 비슷하게 생긴(상태 정보가 추가된) live object configuration 을 쿠버내에 생성
- 또한 json 파일로 변환된 last applied configuration 파일도 저장됨
- 이 세가지 파일(로컬 yaml, last applied configuration, live object configuration) 이 비교되면서 실제 오브젝트에 어떤 변화를 줄지 정해짐
- 예를 들어 로컬 파일에서 이미지를 `nginx:1.19` 로 변경하고 apply 커맨드를 날리면, 먼저 쿠버 클러스터에 있는 live object configuration 과 비교되어 차이점이 발견되고 업데이트 됨
- last applied 파일도 이에따라 최신 값으로 업데이트 됨
- local file: 디스크에 있는 파일
- live object configuration: 클러스터 메모리에 있는 정보
- last applied configuration 은 apply 커맨드를 사용했을때만 annotation 에 기록이 남음
