---
layout: post
title: kube proxy
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

kube proxy에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## Kube Proxy?

- 클러스터 내에 모든 pod는 다른 pod에 접근할 수 있음
- 이는 `pod networking solution` 이라는 클러스터 내부 가상 네트워크 때문에 가능함
- pod의 주소는 계속 바뀔 수 있기 때문에 실제로 네트워킹을 위해서는 `service`를 통해 통신함
- `service`의 ip 혹은 이름과 포트를 통해 다른 pod가 접근할 때 이를 해당되는 pod로 라우팅 해줌
- `service`는 실재하는 것이 아니기 때문에 위에서 언급한 `pod network`에 접근할 수 없음. 그러면 어떻게 통신하는 걸까?
- 바로 `kube proxy`가 여기서 역할을 해줌
- `kube proxy`는 각 노드에서 실행되는 프로세스로, 새로운 `service`가 생성되면 적절한 규칙을 만들어서 노드에서 해당 트래픽이 서비스에서 pod로 포워딩되도록 함
- 그 중 한 방법이 `IP TABLE` 방법
- 이 경우엔 각 노드마다 IP TABLE 룰을 만들어서 서비스의 ip로 들어오는 트래픽을 실제 pod의 ip로 포워딩 해줌

### 설치

- `kubeadm`을 통해 설치하면 daemonset으로 뜨기 때문에 각 노드 당 한 개씩 뜸