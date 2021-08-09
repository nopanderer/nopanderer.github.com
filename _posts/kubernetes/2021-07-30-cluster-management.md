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

## 클러스터 업그레이드

- kube api server 가 핵심 컴포넌트이기 때문에 다른 컴포넌트들은 이것보다 높은 버전을 사용할 수 없음
- kube api server 버전이 X(v1.10)라면,
    - controller manager 와 kube scheduler 버전은 최소 X-1(v1.9 or v1.10)
    - kubelet과 kube-proxy는 최소 X-2(v1.8, v1.9, or v1.10)
    - kubelet은 X-1 ~ X+1(v1.9, v1.10, or v1.11)
- 쿠버네티스는 최신 버전에서 두 단계 아래까지만 지원함
    - 예를 들어 현재 최신 버전이 v1.13 이면 v1.12와 v1.11까지만 지원
- 업그레이드는 한 버전씩 업그레이드 하는 것을 권장
    - v1.10에서 v1.13으로가 아닌 v1.11로

### kubeadm 업그레이드 방법

- 마스터 노드 먼저 업그레이드 후 워커 노드 업그레이드
- 마스터 노드 업그레이드
    - 마스터 노드는 잠시 down
    - 워커 노드나 워커 노드에 떠 있는 pod 들에 영향이 가진 않음
    - 컨트롤 플레인이 제역할을 하지 못하기 때문에 replicaset 으로 떠 있는 pod가 죽는 경우 되살리지 못함
- 워커 노드 업그레이드
    - 전체 노드 한꺼번에 업그레이드 혹은 하나씩 업그레이드
    - 하나씩 업그레이드 하는 경우 해당 노드의 pod 들이 다른 노드로 가서 뜨기 때문에 downtime 이 없음
    - 아니면 업그레이드 된 버전의 노드를 추가하고 예전 버전의 노드는 삭제하는 식으로 업그레이드 할 수도 있음

업그레이드를 위한 명령어

```
kubeadm upgrade plan
```

- 현재 컴포넌트 버전과 최신 버전을 알려줌
- 수동으로 업그레이드 해야 할 kubelet 버전도 알려줌
- 업그레이드를 실행할 명령어도 알려줌
- 컴포넌트 업그레이드 전에 kubeadm 도 업그레이드 해줘야함

### 업그레이드 과정(v1.11 -> v1.12)

kubeadm 업그레이드

```
apt install -y kubeadm=1.12.0-00
```

컴포넌트 업그레이드

```
kubeadm upgrade apply v1.12.0
```

kubectl 로 노드 버전을 체크해도 예전 버전인 것을 알 수 있는데 이는 과거에 노드 등록할 때 api server 버전이기 때문

```
kubectl get nodes
```

마스터 노드 kubelet 업그레이드

```
apt install -y kubelet=1.12.0-00

systemctl restart kubelet
```

워커 노드 한 개씩 업그레이드

```
# move pod
kubectl drain node01
```

```
apt install -y kubeadm=1.12.0-00
apt install -y kubelet=1.12.0-00
kubeadm upgrade node
systemctl restart kubelet
```

```
kubectl uncordon node01
```

다른 워커 노드에서도 같은 작업 반복

## 백업과 복구

### 백업 대상

- 각종 오브젝트
- etcd 클러스터
- Persistent Volume

### 오브젝트 설정 정보 저장 (pod, deploy, svc 만 가능)

```
kubectl get all -A -o yaml > all-deploy-service.yaml
```

### etcd 백업

- etcd.service 를 살펴보면 `--data-dir` 옵션을 볼 수 있음
- 여기에 저장된 데이터를 이용해 백업이 가능
- 혹은 `etcdctl` 를 이용해 스냅샷을 얻을 수 있음
- `etcdctl snapshot save` 시 반드시 다음 옵션들을 주어야 함
    - `--endpoints`: etcd 클러스터 (etcd 가 실행 중인 노드에서 실행할거면 생략해도 됨)
    - `--cacert`
    - `--cert`: etcd 서버용
    - `--key`

```
ETCDCTL_API=3 etcdctl snapshot save snapshot.db
```

복구 (스냅샷)

```
service kubeapi-server stop
```

```
ETCDCTL_API=3 etcdctl snapshot restore snapshot.db --data-dir /var/lib/etcd-from-backup
```

```
systemctl daemon-reload
service kubeapi-server restart
```

바뀐 data-dir 로 변경하기

```
vi /etc/kubernetes/manifest/etcd.yaml
```

volumes.hostPath 에서 `/var/lib/etcd-from-backup`으로 변경

