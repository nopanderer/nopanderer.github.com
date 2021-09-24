---
layout: post
title: "Katib 설치"
categories: mlops
---

Katib 를 설치합시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## 필요 사항

* `kubernetes` >= 1.17
* `kustomize` >= 3.2

## Kustomize 설치

Katib 를 설치하기 위해서는 `kustomize` >= 3.2 설치가 필요합니다.

```
curl -s "https://raw.githubusercontent.com/kubernetes-sigs/kustomize/master/hack/install_kustomize.sh"  | bash
```

바이너리 파일을 `/usr/local/bin/`으로 옮깁시다.

```
sudo mv kustomize /usr/local/bin/
```

아래와 같이 나오면 성공

![kustomize-install](/assets/img/kustomize.png)

## 동적 볼륨 잡아주기

로컬 디렉토리를 이용해서 `dynamic volume provisioner`을 생성합니다.

```
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
```

local-path 스토리지를 기본 스토리지 클래스로 지정합니다.

```
kubectl patch storageclass local-path -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## Katib 설치

```
git clone https://github.com/kubeflow/katib
make deploy
```

## 설치 확인

```
kubectl get pods -n kubeflow
```

![katib-install](/assets/img/katib-install.png)

## 삭제

```
make undeploy
```

## 참고

* https://www.kangwoo.kr/2020/02/18/pc%ec%97%90-kubeflow-%ec%84%a4%ec%b9%98%ed%95%98%ea%b8%b0-3%eb%b6%80-kubeflow-%ec%84%a4%ec%b9%98%ed%95%98%ea%b8%b0/