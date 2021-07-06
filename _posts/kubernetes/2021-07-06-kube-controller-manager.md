---
layout: post
title: kube controller manager
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

kube controller manager에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Kube Controller Manager?

- 클러스터 내에 컴포넌트들을 지속적으로 모니터링하면서 관리함
- 전체적인 시스템이 적절한 상태(desired state)를 유지하도록 하는 역할
- 다양한 controller들이 있지만 몇 가지만 살펴보자.

### node controller

- api server를 통해 노드들의 상태를 점검하고 관리함
- api server를 통해 5초마다 모든 노드의 상태를 체크함
- 노드로부터 heartbeat을 받지 못한 지 40초가 지나면 `unreachable` 상태로 마킹
- `unreachable` 상태 이후 5분이 지났는데도 반응이 없으면 해당 노드에 할당된 pod들과 provision들을 다른 노드로 옮김 (해당 pod들이 replica set로부터 생성된 경우)

### replication controller

- replica set의 상태를 모니터링하고 적절한 수의 pod가 잘 떠 있는지 체크하고 그렇지 않은 경우 수에 맞게 띄워 줌

## 옵션

- 컨트롤러들이 제대로 동작하지 않는다면 kube controller manager의 설정 정보를 살펴보자

```
cat /etc/kubernetes/manifests/kube-controller-manager.yaml
```
