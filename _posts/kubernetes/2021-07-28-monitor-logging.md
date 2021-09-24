---
layout: post
title: "쿠버네티스 모니터링 & 로깅"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

쿠버네티스 모니터링과 로깅 에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 모니터링

- 현재로서는 쿠버에서 fully 지원하는 모니터링 솔루션이 없음
- 하지만 다양한 오픈소스들이 있음: Metric Server, Prometheus, Elastic Stack 등

### Metric Server

- Headster 가 전신
- 쿠버 클러스터 상에 있는 노드와 pod들로부터 메트릭 정보를 수집해 메모리에 보관함
- 인메모리 솔루션이기 때문에 히스토릭한 정보는 볼 수 없음 (프로메테우스나 Elalstic stack 을 쓰자)
- kubelet 은 하위 컴포넌트로 `cAdvisor` 가 있음
- `cAdvisor`는 pod로부터 메트릭 정보를 받고 kubelet api 를 통해 Metric Server 에 전달함

### Metric Server 설치 및 사용

```
git clone https://github.com/kubernetes-incubator/metrics-server.git

kubectl create -f deploy/1.8+/
```

아래 명령어로 메트릭을 볼 수 있음

```
kubectl top node
```

혹은

```
kubectl top pod
```

## 로깅

### 도커에서 로깅

```
docker logs -f {container-id}
```

### 쿠버네티스에서 로깅

```
kubectl logs -f {pod-name}
```

pod 안에 여러 개의 컨테이너가 실행 중인 경우

```
kubectl logs -f {pod-name} {container-name}
```
