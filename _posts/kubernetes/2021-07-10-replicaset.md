---
layout: post
title: "[쿠버네티스 기초 10] ReplicaSet"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

ReplicaSet에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## ReplicaSet 이 필요한 이유

- 정해진 수만큼의 Pod를 항상 유지시켜 줌
- Pod가 하나만 있는 경우, 예기치 못한 상황(장애 등)에서 대처할 수가 없음
- Pod 를 여러 개 두어 availability 를 높이기 위해 replication controller를 사용
- Pod 가 한개일 경우라도 replication controller 는 유용함. 장애 시에 새로운 pod를 다시 띄워주기 때문
- 또한 트래픽이 증가하는 경우에, pod 수를 늘려서 트래픽을 분산할 수 있음(여러 노드에 걸쳐서)

## vs. Replication Controller

- repliacation controller는 예전 컨트롤러로 replica set에 의해 대체됨

## replication controller 정의 및 배포

- `spec/template` 섹션을 통해 replica에 대해 정의
- pod 정의하는 yaml 파일에서 `apiVersion`과 `kind`만 빼고 그대로 `spec/template`에 넣으면 됨
- `template`과 같은 레벨에 `replica` 정보 삽입

```yaml
apiVersion: v1
kind: ReplicationController
metadata: # for rc
  name: myapp-rc
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata: # for pod
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-controller
        image: nginx  
  replica: 3
```

`kubectl`로 배포

```
kubectl create -f rc-definition.yaml # 배포
kubectl get rc # 확인
```

## replicaset 정의 및 배포

- rc 배포할 때랑 매우 비슷
- 추가적으로 `selector` 항목이 필요(어떤 pod를 관리해야 하는지)
- 이 `selector`로 인해 rs으로 만들어지지 않은 pod까지 관리할 수 있음

```yaml
apiVersion: apps/v1
kind: ReplicationSet
metadata: # for rs
  name: myapp-rs
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata: # for pod
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
      - name: nginx-controller
        image: nginx  
  replica: 3
  selector:
    matchLabels:
      type: front-end
```

`kubectl`로 배포

```
kubectl create -f rs-definition.yaml
kubectl get rs
```

## Label 과 Selector

- 쿠버네티스를 운영하다 보면 수많은 pod가 생기게 됨
- replicaset은 pod 생성 시에 작성한 `label` 정보를 `selector` 를 통해 필터링 함(감시할 pod만 필터링)
- 이와 같은 컨셉이 rs말고도 다른데서도 사용됨

## Scale

- 스케일 업/다운을 하기 위해서 여러 방법이 있음

### kubectl replace

- yaml 파일에서 replica 수를 변경하고 아래 커맨드 날림

```
kubectl replace -f rs-definition.yaml
```

### kubectl scale

스케일 커맨드로 변경 (파일 내용이 바뀌진 않음)

```
kubectl scale --replica=6 -f rs-definition.yaml
```

혹은 이름으로 지정

```
kubectl scale --replica=6 rs myapp-rs
```
