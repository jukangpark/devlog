---
description: 아니 어떻게 reIssue 하고 다시 재요청해요?
cover: >-
  https://images.unsplash.com/photo-1639152201720-5e536d254d81?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw3fHx0b2tlbnxlbnwwfHx8fDE3MjA1OTQwNDB8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 주니어가 토큰 관리를 내맘대로? 어떻게?

### JWT 토큰이란

[JWT (Json Web Token)](https://jwt.io/introduction) 토큰은 클라이언트와 서버 간에 정보를 안전하게 전송하기 위한 컴팩트하고, 독립적인 방식의 토큰이다. JWT 는 JSON 객체를 사용하여 정보를 저장하며, 이 정보는 디지털 서명되어 있어 인증 및 정보 무결성을 보장할 수 있다.



#### JWT 의 구성 요소

JWT 는 3가지의 부분으로 구성되어있다.

1. 헤더 (Header)
2. 페이로드 (Payload)
3. 서명 (Signature)

이 세 부분은 각 Base64Url 로 인코딩되며, 점(.) 으로 구분된다.





### 헤더 (Header)

JWT 의 유형과 서명 알고리즘을 지정한다.\
ex)

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

Base64Url 로 인코딩된 헤더

```json
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
```





### 페이로드 (Payload)

JWT 가 저장하는 클레임(Claim)을 포함한다. 클레임은 토큰에 담고자 하는 정보이며, 보통 다음과 같은 세 가지 유형으로 나뉜다.



1. 등록된 클레임 (Registered claims): iss(발급자), exp(만료시간), sub(주제), aud(청중) 등
2. 공개 클레임 (Public claims) : 충돌을 방지하기 위해 IANA JSON Web Token Registry 또는 URI 형식을 통해 정의된 클레임
3. 비공개 클레임 (Private claims) : 양 당사자 간에 사용하기 위해 정의된 클레임

ex)

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
```

Base64Url로 인코딩된 페이로드&#x20;

```
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ
```





### 서명 (Signature)

서명을 통해 토큰의 무결성과 출처를 검증할 수 있다. \
서명은 헤더, 페이로드, 그리고 비밀 키를 사용하여 생성된다.

• 서명 생성 방법:

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret)
```

ex)

```
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```





### AccessToken vs RefreshToken









### 요청과 응답을 가로채자







### AccessToken 재발급 받고 어떻게 다시 ajax 요청할까?





