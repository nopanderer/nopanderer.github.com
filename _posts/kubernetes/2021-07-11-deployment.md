---
layout: post
title: "[쿠버네티스 기초 11] Deployment"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

Deployment에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Deployment

- 새로운 버전의 어플리케이션을 배포하고 싶은 경우에 사용(무중단 배포)
- 이전 버전의 pod를 하나씩 내리면서 새로운 버전의 pod를 하나씩 올림(rolling update)
- 또한 새롭게 배포한 어플리케이션에 문제가 있는 경우 롤백도 가능함
- 기존의 어플리케이션을 잠시 멈추었다가 업데이트를 적용하고 resume하는 기능도 제공
- replication controller, replicaset 보다 상위 오브젝트(deploy > rs,rc > pod)

## 정의

- replicaset의 yaml과 apiVersion 빼고 똑같음
- deployment를 배포하면 replicaset도 자동으로 만들어짐

```
kubectl get deploy
kubectl get rs
```

아니면 아래 명령어로 한꺼번에 다 보기

```
kubectl get all
```





