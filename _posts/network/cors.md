# CORS (Cross Origin Resource Sharing)

**도메인** 또는 **포트**가 다른 서버의 자원을 요청하는 메커니즘이다. 기본적으로는 동일 출처 정책(same-origin-policy) 때문에 CORS과 같은 상황이 발생하면 요청한 데이터를 브라우저에서 보안목적으로 차단한다. 프론트에서 백단의 API를 호출해야 하는 경우, 백단에서 cross-origin http 요청을 허가해 주어야 한다.

## django-cors-headers

장고 패키지 설치

```sh
pip install django-cors-headers
```

`settings.py` 에서 `INSTALLED_APPS`와 `MIDDLEWARE`에 추가

```python
INSTALLED_APPS += 'cors_headers',
MIDDLEWARE += 'corsheaders.middleware.CorsMiddleware',
```

`CORS_ORIGIN_WHITELIST`에 프론트 주소 추가

```python
CORS_ORIGIN_WHITELIST = (
    'http://localhost:3000',
)
```