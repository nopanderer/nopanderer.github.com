---
layout: post
title: "쿠버네티스 클러스터 관리"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

쿠버네티스 클러스터 관리에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 노드에 대한 작업이 필요한 경우

- 노드가 다운되는 경우, 쿠버네티스는 디폴트로 5분을 기다려줌
    - 5분 내에 복구되는 경우 pod도 다시 뜸 (by kubelet)
    - 5분 지나도 복구 안되는 경우, 해당 노드가 죽은 것으로 판단하고 해당 pod 들을 다른 노드에 띄움
    - 5분 뒤에 노드가 복구되더라도 이미 옮겨진 pod 들은 돌아오지 않음
- 이 시간은 컨트롤러 매니저를 실행 시에 옵션으로 줄 수가 있음

```
kube-controller-manager --pod-eviction-timeout=5m0s ...
```

- 단 5분 이후에 복구되거나 이동되는 pod 는 replicaset 에 의해 생성된 pod 일 경우만 해당됨. 그냥 pod 는 5분 뒤에 사라짐

### drain 명령어

- 특정 노드에 작업으로 인해 pod 들을 내려야 할 경우에는 `drain` 명령어를 사용하는 것이 좋음
```
kubectl drain node01
```
- 이 명령어를 실행하면 해당 노드는 스케줄링에서 제외되며, 해당 노드에 떠있는 pod 들을 종료하고 다른 노드들에 띄움
- 이때 이동되는 pod 들은 replicaset 에 의해 생성된 pod 뿐만 아니라 모든 pod 들이 해당됨
- 노드에 대한 작업이 끝나면 `uncordon` 명령어를 이용해 클러스터로 복귀시킴. 복귀되더라도 이미 이동된 pod 들은 돌아오지 않음
```
kubectl uncordon node01
```

### cordon과 drain

```
kubectl cordon node01
```
- `cordon` 명령어는 해당 노드를 스케줄링에서 제외시키고 싶을 때 사용함
- `drain`과의 차이는 현재 떠있는 pod 들에는 영향이 없다는 것
- 따라서 명령어 적용 이후에 새롭게 생성되는 pod 들이 이 노드에 스케줄링되지 않음

## 쿠버네티스 버전

- 쿠버네티스는 버전이 세 개의 파트로 구성되어 있음
- v{MAJOR}.{MINOR}.{PATCH}
    - MINOR는 기능 추가 등
    - PATCH는 버그 수정 등
- ETCD와 CoreDNS 는 따로 독립된 버전으로 개발
