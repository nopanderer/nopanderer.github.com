---
layout: post
title: "쿠버네티스 라이프사이클"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

쿠버네티스 라이프사이클 에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 롤링 업데이트와 롤백

- deployment 를 생성하고 후에 도커 이미지 업데이트로 인해  deployment 를 수정하면 새로운 revision이 생김
- revision 들로 인해 변화점들을 추적할 수 있고 문제가 생겼을 때 이전 revision으로 롤백할 수 있음

### 롤아웃 명령어

```
kubectl rollout status deploy {deploy-name}
```

히스토리 정보

```
kubectl rollout history deploy {deploy-name}
```

### Deployment 배포 종류

- Recreate
    - 기존 pod 를 전부 내리고, 새로운 pod 를 올림
    - 새로운 pod 가 뜨기 전까지 서비스가 불가하다는 단점

-  RollingUpdate (DEFAULT)
    - 내리고 올리는 과정을 하나씩 진행
    - 서비스 중단 없음

### Deployment 업데이트

- yaml 파일을 수정하고 `kubectl apply -f` 커맨드로 새로운 rollout를 만듦
- `kubectl set image` 커맨드 사용

```
kubectl set image deploy {deploy-name} {container-name}=nginx:1.9.1
```

### 롤링 업데이트 동작 과정

- 새로운 deploy가 생성되면 그에 따른 replicaset이 생성됨
- deploy가 수정되면 여기에 맞는 새로운 replicaset이 만들어짐
- old replicaset 에서는 pod를 하나씩 내리면서 new replicaset 에서는 pod를 하나씩 생성

### 롤백

- 새로운 deploy에 문제가 있어서 이전 버전으로 되돌리고 싶은 경우 롤백 사용

```
kubectl rollback undo deploy {deploy-name}
```

## command와 args

- 도커 커맨드 뒤에 append 하고 싶은 경우 `args` 필드를 작성
- 도커파일 내에 작성된 CMD 부분을 replace 하게 됨
- 도커파일의 ENTRYPOINT 를 replace 하고 싶은 경우 `command` 필드 작성
- 정리하면 Dockerfile 의 ENTRYPOINT는 `spec.containers.command`와 CMD는 `spec.containers.args`와 대응됨

```yaml
...
spec:
  containers:
  - name: ubuntu-sleeper
    image: ubuntu-sleeper
    command: ["sleep2.0"]
    args: ["10"]
```

## 환경 변수

definition 파일에서 `spec.containers.env` 필드를 작성

```Dockerfile
...
spec:
  containers:
  - name: sample
    ... 
    env:
    - name: APP_COLOR 
      value: pink
```

`configMap` 이나 `Secret` 통한 방법도 있음

```Dockerfile
  env:
  - name: APP_COLOR
  - valueFrom:
      configMapKeyRef:
```

```Dockerfile
  env:
  - name: APP_COLOR
    valueFrom:
      secretKeyRef: 
```