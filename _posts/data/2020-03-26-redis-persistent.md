---
layout: post
title: "[Redis] 영속성 AOF, RDB"
categories: data
---

1. this unordered seed list will be replaced by toc as unordered list
{:toc}

## AOF (Append Only File)

- 디폴트로 `appendonly.aof` 파일에 기록
- 입력/수정/삭제 명령이 실행될 때마다 append 로 기록
- 특정 시점에 rewrite 함. 계속 append 만 하다보면 파일 사이즈가 커지기 때문에 일종의 최적화

### AOF 옵션

- `--appendonly yes` 옵션을 줌
- `--appendfilename`: aof 파일 이름 지정 가능
- `--appendfsync`: AOF에 기록하는 시점을 정함. `always`는 명령 실행마다 기록
- `--auto-aof-rewrite-percentage 100`: 최초 레디스 서버 시작 시 파일 사이즈 기준으로 100이면 rewrite 함
- `--auto-aof-rewrite-min-size 64mb`: 64mb 이하일시 rewrite를 하지 않음

## RDB (snapshot)

- 특정 시점에 메모리에 있는 데이터 전체를 바이너리 파일로 저장
- AOF 보다 파일 사이즈가 작아서 로딩 속도가 더 빠름
- `redis.conf` 에서 save 파라미터로 조건을 설정

아래 설정은 300초 동안 10번 이상 key 변경이 발생하면 저장

```
# save <seconds> <changes>
save 300 10
```

## 참고

- [http://redisgate.kr/redis/configuration/persistence.php](http://redisgate.kr/redis/configuration/persistence.php)
