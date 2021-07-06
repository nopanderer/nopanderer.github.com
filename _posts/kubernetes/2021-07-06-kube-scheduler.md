---
layout: post
title: kube scheduler
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

kube scheduler에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Kube Scheduler

- 특정 Pod가 어느 노드에 떠야되는지 결정 (자원, 라벨 등을 기준으로)
- 결정만 하는거지 실제로 띄우는 건 kubelet이 함

## 동작 과정

1. Pod의 조건(자원, 라벨 등)을 보고 이에 맞지 않는 노드를 제외함
2. priority function을 통해 후보 노드들에 점수를 매김. 자원을 예로 들면 해당 pod를 배치하고도 남는 자원이 많은 노드에 큰 점수를 줌.

## 옵션

아래 명령어를 통해 옵션을 확인

```
cat /etc/kubernetes/manifests/kube-scheduler.yaml
```
