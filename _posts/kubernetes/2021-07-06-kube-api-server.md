---
layout: post
title: [쿠버네티스 기초] kube api server
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

kube api server에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## kube api server?

- 쿠버네티스에서 주요 관리 컴포넌트
- `kubectl`을 실행하면 실제로 kube api server와 통신
- kube api server는 요청에 대한 인증을 검사하고 etcd로부터 요청한 데이터를 리턴함
- 반드시 `kubectl`을 쓸 필요는 없음. API를 직접 api server에다가 때려도 됨

## 동작 과정

다음과 같이 pod를 생성하는 요청이 들어 왔다고 하자.

```
curl -X POST /api/v1/namespaces/default/pods ...
```

1. api server가 요청을 검증 (유저 검증, 요청 검증)
2. pod 오브젝트를 생성 (노드에 할당은 아직 안함)
3. etcd에 업데이트
4. 유저에게 업데이트 메시지 날림
5. `scheduler`가 api server를 모니터링 하던 중 노드에 할당되지 않은 새로운 pod가 생성되었음을 확인하고 pod를 할당할 적절한 노드를 찾음
6. 그리고 그 사실을 api server에 보고함
7. api server는 이 정보를 etcd에 업데이트 함
8. api server는 scheduler가 찾은 이 노드의 kubelet에게 이 정보를 전달함
9. `kubelet`은 자신의 노드에 pod를 생성하고 container runtime engine(`Docker`)에게 해당 이미지를 배포하도록 함
10. 일이 끝나면 `kubelet`은 이 결과를 api server에게 보고하고, api server는 이 정보를 etcd에 반영함

대부분의 요청이 이런 과정을 거치게 됨

이처럼 `kube api server`은 동작 과정 중심에 있음

## kube api server 이 하는 일 요약

- 요청을 인증 및 검증
- etcd로부터 데이터를 조회 혹은 업데이트 (etcd와 소통하는 유일한 컴포넌트)
- scheduler, kubelet 등은 클러스터에 특정 작업을 하기 위해 반드시 api server를 거침

## `kubeadm`없이 설치

- etcd 설치 떄와 마찬가지와 바이너리로 설치함
- 여러 다른 컴포넌트들과 통신해야 하기 때문에 많은 인증서 설정이 필요
- 다양한 설정 정보를 넘겨줘야 하는데, 대부분이 인증 관련
- `etcd` 와의 통신을 위해 해당 정보도 설정해줘야 함

```
...
--etcd-servers=https://127.0.0.1:2379 \\
...
```

## `kubeadm`을 통해서 설치

아래 명령어를 통해 잘 띄워져있음을 볼 수 있다.

```
kubectl get po -n kube-system
```

아래 경로에서 api server의 설정 파일을 볼 수 있음

```
cat /etc/kubernetes/manifests/kuber-apiserver.yaml
```
