---
layout: post
title: "Static Pod"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

Static Pod 에 대해 알아봅시다.

0. this unordered seed list will be replaced by toc as unordered list
{:toc}

## 마스터 플레인없이 Kubelet 만 있다면 pod 를 띄울 수 있을까?

- kubelet 은 api-server 와 통신을 통해 pod 생성/삭제를 함
- kube api server 없이도 이것이 가능할까?
- `/etc/kubernetes/manifest` 디렉토리에 매니패스트 파일을 넣어놓으면 kubelet이 이를 주기적으로 읽어서 pod 를 띄울 수 있음
- 단순히 pod 생성 뿐만 아니라 유지까지 해줌
- 이 파일에 수정을 하면 자동으로 해당 pod 를 재생성함
- 파일이 위 디렉토리에서 삭제되면 pod 도 삭제됨
- 이렇게 master plane 없이 생성되는 pod 들을 **Static Pod**라고 부름
- 유의해야 할 점은 **Pod**만 생성이 가능 (rs, deploy 등 다른 오브젝트들은 전부 불가)

## 매니페스트 디렉토리 설정

- kubelet 서비스를 실행할 때 옵션(`--pod-manifest-path`)으로 넣을 수 있음. 아니면 `--config` 옵션으로 파일을 넘겨도 됨. 여기서 `staticPodPath`로 `/etc/kubernetes/manifests` 로 설정.
- kubeadm 으로 설치한 경우 후자 방식을 사용하는데, `/var/lib/kubelet/config.yaml` 을 확인해보면 됨
- static pod 는 `docker ps` 로 확인할 수 있음 (api server가 없음을 가정했기에)
- kubelet 은 api-server 가 있더라도, static pod 를 생성할 수 있음 (즉 pod 생성 방식이 2개). 이 경우에도 kubectl 커맨드로 static pod 를 확인할 수 있음
- kubelet 이 static pod를 생성하고 kube api server에 read only 미러 오브젝트를 생성하기 때문. 따라서 수정/삭제가 불가

## Static Pod 는 언제 쓸까?

- control plane에 독립적이기 때문에 control plane 컴포넌트 자체를 띄우고 싶을 때 쓸 수 있음 (HA를 위해 마스터를 여러개 만들고 싶은 경우 등)
- 각 마스터 노드에 kubelet을 설치하고, manifest 경로에 control plane component 매니페스트 파일을 넣으면 됨
- 실제로 kubeadm 이 이런 방식으로 클러스터를 셋업함
- 이렇게 생성된 static pod은 이름 뒤에 `-노드이름` 이 붙음
