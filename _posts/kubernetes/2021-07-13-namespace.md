---
layout: post
title: "[쿠버네티스 기초 12] Namespace"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

Namespace에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## namespace

- 쿠버네티스 상에서 논리적인 구분 단위
- namespace 없이 배포하게되면 `default` namespace에 배포됨
- 클러스터 운영 상 배포되는 오브젝트들은 `kube-system` namespace에 배포됨
- 모든 유저가 접근 가능한 `kube-public`이란 namespace도 있음
- 클러스터를 사용하는 사람이 많아질수록 namespace 구분을 통해 의도치 않은 행동들을 어느 정도 방지할 수 있음
- 각 namespace는 별도의 policy와(권한) quota(자원)를 가질 수 있음

## DNS

- 타 namespace에 있는 서비스에 접근하는 경우에는 아래와 같이 전체 이름을 명시해야 함(서비스 생성 시 namespace에 맞는 DNS 엔트리가 자동으로 생성)

```
# dev namespace에 있는 서비스에 접근
mysql.connect("db-service.dev.svc.cluster.local")
```

- 여기서 `cluster.local`은 쿠버네티스 클러스터의 도메임 이름
- `svc`는 서비스의 서브 도메인
- `dev`는 namespace
- `db-service`는 서비스 이름

## 배포

- namespace 자체를 생성하고 싶은 경우 아래와 같이 yaml 파일 생성

```
apiVersion: v1
kind: Namespace
metadata:
  name: dev
```

- 아니면 명령형으로 생성

```
kubectl create namespace dev
```

## Switch

디폴트는 default로 잡혀있지만 config 설정을 통해 기본 namespace를 바꿀 수 있음

```
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

## Resource Quota

- 리소스 쿼타를 생성하기 위해 아래와 같이 작성
- namespace와 자원 명시

```
apiVersion: v1
kind: ResouceQuota
metadata:
  name: compute-quota
  namespace: dev
spec:
  hard:
    pods: "10"
    requests.cpu: "4"
    requests.memory: 5Gi
    limits.gpu: "10"
    limits.memory: 10Gi
```
