---
layout: post
title: "쿠버네티스 스케줄링"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

쿠버네티스 스케줄링에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 스케줄링 동작 방식

- 모든 pod 은 `nodeName`이라고 불리는 필드가 존재함
- 보통은 이 필드를 비워두는데, 스케줄러가 적절한 노드를 찾아서 이 값을 채워줌
- `nodeName` 필드 값이 존재하면, 스케줄러는 모든 노드를 훑어보면서 이 필드값에 해당되는 후보 노드들을 검색하고 적절한 노드에 해당 pod를 할당함
- 만약 모든 노드에서 이 필드 값을 발견하지 못하면 pod는 `Pending` 상태가 됨
- 수동으로 특정 노드에 pod를 할당하고 싶으면, 이 `nodeName` 값에 노드 이름을 넣어주면 됨

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
      - containerPort: 8080
  nodeName: node02
```

- `nodeName` 값에 세팅해주는 것은 **오브젝트 생성 시점**에만 가능하다. 추후 변경하는 건 불가
- 기존 pod를 다른 노드에 할당하려면, `Binding` 오브젝트를 사용하면 됨

```yaml
apiVersion: v1
kind: Binding
metadata:
  name: nginx
target:
  apiVersion: v1
  kind: Node
  name: node03
```

- 위 yaml 파일을 json 형태로 바꾸고 해당 pod의 binding api에 POST를 날리면 됨 (상당히 귀찮네...)

```
curl -X POST --data '{"application":"v1", "kind": "Binding" ...} http://$SERVER/api/v1/namespaces/default/pods/$PODNAME/binding/
```

## Label, Selector, Annotation

- 그룹화(label)하고 필터링(selector)하기 위해 사용

```yaml
# pod-definition.yaml
apiVersion: v1
kind: Pod
metadata:
  name: simple-webapp
  labels:
    app: App1
    function: Front-end
...
```

- 다음와 같이 `--selector` 옵션으로 label을 필터링할 수 있음

```
kubectl get pods --selector app=App1
```

- replicaset, service 같은 오브젝트에서는 selector를 활용해 pod를 관리
- annotation은 label과 달리 메모 용도로 정보를 적기 위해 사용

```yaml
...
metadata:
  name: simple-webapp
  labels:
    name: App1
    function: front-end
  annotations:
    buildversion: 1.34
...
```

## Taint 와 Toleration

- pod 들이 노드에 스케줄링 되지 않도록 제한을 가할 때 쓰임

### 벌레와 스프레이의 예

- 여기서 벌레가 pod 고 사람이 node
- node 에 A라는 스프레이를 뿌린다(taint)
- 이 A라는 스프레이에 면역(toleration)이 없는 벌레들은 사람 몸에 앉을 수 없음

### 다시 쿠버네티스 관점에서

![taint](/assets/img/k8s-taint.png)

- 위 그림처럼 node1에 `taint=blue`와 같이 taint 를 가했을 때, 이에 대한 toleration이 없는 pod 들(A, B, C)은 스케줄링 되지 않음
- pod D만 node1에 배치하고 싶으면, `taint=blue` 에 대한 toleration 을 주면 됨
- 헷갈리면 안되는 점은 pod D 가 반드시 node1에 배치되는게 아님. node01 에 배치될 수 있는 toleration 을 가진 것 뿐이기 때문에 다른 노드에 배치될 수도 있음
- 특정 노드에 배포하고 싶은 경우에는 `nodeSelector`를 사용하면 됨
- 정리하면, `taint`는 node에 `toleration`은 pod에

### taint 하기

```
kubectl taint nodes node-name key=value:taint-effect
```

예제

```
kubectl taint nodes node01 app=blue:NoSchedule
```

- 여기서 `taint-effect`는 이 taint 를 tolerate 하지 못하는 pod들이 취해야 할 행동을 말함
- 세 가지 effect가 있음 (NoSchedule, PreferNoSchedule, NoExecute)
- NoSchedule: 앞으로 이 노드에 스케줄링되지 않음
- PreferNoSchedule: 최대한 이 노드에 스케줄링 되지 않도록 하지만 보장하진 않음
- NoExecute: 앞으로 뿐만 아니라 현재 떠있는 pod 들도 검사해서 조건에 안 맞으면 내쫓음

### taint 없애기

```
kubectl taint nodes master node-role.kubernetes.io/master-
```

### toleration 하기

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: nginx-controller
    image: nginx
  tolerations:
  - key: "app"
    operator: "Equal"
    value: "blue"
    effect: "NoSchedule"
```

- 반드시 toleration 값들은 쌍따옴표로 감싸야함

## Node Selector

- pod 를 특정 노드에 띄우고 싶을 때 사용
- yaml 파일 spec에 `nodeSelector` 를 추가하면 됨(노드의 label 과 매칭시킴)
- 노드 선택 조건이 복잡한 경우(OR나 NOT 같은 경우) node selector로는 처리가 어려움
- 이런 경우에는 node affinity 나 anti affinity 를 사용함

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: data-proc
    image: data-proc
  nodeSelector:
    size: Large
```

### 노드에 label 붙이기

```
kubectl label nodes <node-name> <label-key>=<label-value>
```

예시

```
kubectl label nodes node-1 size=Large
```

## Node Affinity

- node selector 과 마찬가지로 pod 가 특정 노드에 뜨도록 하는 역할
- node selector 로는 처리가 어려운 (조건이 복잡한 경우) 조건도 처리 가능
- `nodeAffinity` 밑에 문장과 같은 것이 오고, `nodeSelectorTerms` 밑에 리스트로 조건을 넣어줌
- 다양한 operator 가 있음 (Exists, NotIn 등)
- 현재 생성되는 pod의 label이 조건에 맞지 않거나, pod 가 생성된 이후에 수정이 돼서 label 조건이 맞지 않으면 어떻게 처리될까? node affinity 타입이 이에 대한 답

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
spec:
  containers:
  - name: data-proc
    image: data-proc
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: size
            operator: In
            values:
            - Large
            - Medium
```

### Node Affinity 타입

- nodeAffinity에 대해 스케줄러가 어떻게 동작할지에 대해 정의함
- 현재는 두 가지 타입만 존재
  - requiredDuringSchedulingIgnoredDuringExecution: 생성 당시 반드시 조건에 맞아야 됨. 만들어진 이후에는 label 신경 안씀
  - preferredDuringSchedulingIgnoredDuringExecution: 조건에 안 맞는 노드가 있어도 띄울 수 있음. 만들어진 이후에는 label 신경 안씀

## Resource requirements and limits

- 각 pod 마다 실행을 위해 리소스 자원을 필요로 함(cpu, memory 등)
- 스케줄러가 이를 고려해서 노드에 스케줄링 하게 됨
- 모든 노드가 해당 pod 의 리소스를 수용할 수 없으면, 그 pod 는 `Pending` 상태가 됨

### Resource Requests

- 각 pod 는 디폴트로 0.5 cpu, 256Mi 메모리를 요구함 
- 이를 수정하려면 yaml 파일에서 직접 명시하면 됨

```yaml
...
spec:
  containers:
    ...
    resources:
      requests:
        cpu: 1
        memory: "1Gi"
```

- cpu 는 0.1 혹은 1m 이 최소
- cpu 1 = 1 vCPU (스레드 1개)
- 메모리의 경우, 1 M (Megabyte, 1,000,000 bytes)와 1 Mi (Mebibyte, 1,048,576 bytes) 는 다름

### Resource Limits

- 도커에서는 컨테이너에 리소스 제한이 없음 (호스트의 모든 자원을 잠식할 수도 있음)
- 쿠버에서는 컨테이너마다 디폴트로 1 vCPU, 512Mi 로 제한함

```yaml
...
spec:
  containers:
    ...
    resources:
      limits:
        cpu: 2
        memory: "2Gi"
```

- cpu 를 넘게 되면 쓰로틀링을 걸어서 limit 이상 사용할 수 없음
- 메모리는 limit 을 넘을 수 있음. 계속 넘게 되면 해당 pod 는 종료됨
- namespace에 `LimitRange` 오브젝트를 만들어서 디폴트로 지정할 limit과 request를 세팅할 수도 있음

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: mem-limit-range
spec:
  limits:
  - default:
      memory: 512Mi
    defaultRequest:
      memory: 256Mi
    type: Container
```

## Multiple Schedulers

- 오브젝트를 생성할 때 커스텀 스케줄러를 통해 노드에 배치할 수도 있음
- 따라서 쿠버네티스 내에 디폴트 스케줄러 뿐만 아니라 커스텀 스케줄러들도 같이 존재할 수 있음

### 추가적인 스케줄러 배포

- 프로세스 조회를 통해 옵션 정보를 확인할 수 있음

```
ps -ef | grep kube-scheduler
```

- `--scheduler-name` 옵션을 주지 않으면 default-scheduler 라는 이름으로 생성됨
- 추가적인 스케줄러를 생성하고 싶으면, 기존 kube-scheduler 바이너리 파일이나 자체적으로 만든 것을 통해 이름만 달리해서 생성하면 됨. 이 이름을 활용해서 pod definition 파일에서 스케줄러를 설정함

### kubeadm 으로 추가적인 스케줄러 배포

- `/etc/kubernetes/manifests` 하위에 kube-scheduler.yaml 파일을 그대로 복사해서 `--scheduler-name` 이름만 바꿔서 `kubectl`로 띄우면 됨
- `--leader-elect` 옵션은 클러스터 내에 여러 개의 스케줄러가 존재할 때(HA 구성 같은 경우), 하나의 스케줄러만이 active 상태가 될 수 있음
- 마스터가 여러 개 인 경우 추가적으로 `--lock-object-name` 이라는 옵션을 추가해서 디폴트 스케줄러와 구분해서 리더를 선출함

### 커스텀 스케줄러 사용해서 오브젝트 생성

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: nginx
    image: nginx
  schedulerName: my-custom-scheduler
```

### 이벤트 확인

이벤트 조회를 통해 어떤 스케줄러가 사용되었는지 확인 가능

```
kubectl get events
```
