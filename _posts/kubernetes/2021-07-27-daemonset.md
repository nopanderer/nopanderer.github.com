---
layout: post
title: "[쿠버네티스 기초 15] Daemonsets"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

Daemonset 에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Daemonsets

- 각 노드에 1개씩 pod를 띄우고 싶을 때 사용
- 모든 노드에 1개 씩 pod 가 동작함을 보장함 (노드 추가/삭제와 무관)
- 노드 모니터링이나 로그 수집기 등의 목적에 적합한 오브젝트
- 모든 워커 노드에서 필수적으로 존재하는 kube proxy가 대표적인 daemonset의 예
- 네트워크 에이전트(weavenet, calico 등)도 daemonset의 예

### Daemonset 생성

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: monitoring-daemon
spec:
  selector:
    matchLabels:
      app: monitoring-agent
  template:
    metadata:
      labels:
        app: monitoring-agent
    spec:
      containers:
      - name: monitoring-agent
        image: monitoring-agent
```

### 동작 과정

- 버전 12부터는 node affinity와 디폴트 스케줄러를 통해 각 pod 를 노드에 할당함
