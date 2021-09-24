---
layout: post
title: "[쿠버네티스 기초 16] Configmap"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

Configmap 에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## Configmap

- definition 파일이 많아지면 환경 변수 관리가 어려움
- 환경 변수들을 중앙에서 관리하기 위한 오브젝트
- 환경 변수들을 key value 로 관리함

## 생성

### Imperative

```
kubectl create configmap \
<config-name> --from-literal=<key>=<value> \
--from-literal=<key2>=<value2>
```

혹은 파일로부터

```
kubectl create configmap \
app-config --from-file=app_config.properties
```

### Declarative

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  APP_COLOR: blue
  APP_MODE: prod
```

```
kubectl create -f config-map.yaml
```

## 조회

```
kubectl get cm
```

## 사용

```yaml
...
spec:
  - name:
  ...
  envFrom:
  - configMapKeyRef:
      name: app-config
```

단일 환경 변수로 읽어들일 수 있음

```yaml
...
spec:
  - name:
  ...
  env:
  - name: APP_COLOR
    valueFrom:
      configMapRef:
        name: app-config
        key: APP_COLOR
```
