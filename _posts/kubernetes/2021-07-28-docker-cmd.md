---
layout: post
title: "[도커] CMD와 ENTRYPOINT"
categories: kubernetes
---

이 포스팅은 `Udemy`의 `Certified Kubernetes Administrator with Practice Tests` 강의를 정리한 내용입니다.
{:.note}

도커에서 CMD와 ENTRYPOINT 차이에 대해 알아봅시다.

* this unordered seed list will be replaced by toc as unordered list
{:toc}

## CMD

- 우분투 이미지를 실행하게 되면 실행하자마자 `Exited` 상태가 되어 버림
- 그 이유는 우분투 이미지의 도커파일에서 CMD 가 bash 로 설정되어서 bash 를 수행하고 바로 종료되기 때문
- 이를 변경하는 두 가지 방법이 있음
- 첫번째는 `docker run` 명령어 뒤에 실행하고자 하는 프로세스를 직접 적으면 됨. 이 경우에, 도커파일 내 CMD 명령을 덮어쓰게 됨

```
docker run ubuntu sleep 5
```

- 두 번째 방법은 도커파일 내에서 마지막 CMD 부분을 직접 작성하는 방법

```Dockerfile
FROM ubuntu
...
CMD sleep 5
```

아래와 같이 리스트 형식으로 적어줄 수도 있음

```Dockerfile
...
CMD ["sleep", "5"]
```

## vs. ENTRYPOINT

프로세스의 아규먼트를 런타임에 작성하고 싶다면 어떻게 해야할까?


이때 `ENTRYPOINT` 를 사용하면 된다. `CMD`와 다르게 `ENTRYPOINT`는 컨테이너 실행 시 반드시 실행되기 때문에 뒤이어 아큐먼트 등을 입력할 수 있다.

```Dockerfile
...
ENTRYPOINT ["sleep"]
```

다음과 같이 실행 가능

```
docker run my-image 15
# will sleep 15 seconds
```

**즉, `CMD`는 커맨드 전체가 replace되지만, `ENTRYPOINT`는 뒤에 append 됨**


만약 아큐먼트로 디폴트 값을 주고 싶은 경우에는 이 둘의 조합을 사용하면 됨

```Dockerfile
FROM ubuntu
ENTRYPOINT ["sleep"]
CMD ["5"]
```

도커 실행 시에 ENTRYPOINT 를 바꾸고 싶다면, `--entrypoint` 	옵션을 사용하면 됨

```
docker run --entrypoint sleep2.0 my-image 10
```

