---
description: 어떻게 해야 토큰을 재발행 받고 ajax 재요청 할 수 있는 구조를 프론트에서 구현할 수 있을까?
cover: >-
  https://images.unsplash.com/photo-1639152201720-5e536d254d81?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw3fHx0b2tlbnxlbnwwfHx8fDE3MjA1OTQwNDB8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# JWT 토큰을 활용한 프론트엔드 인증 및 재발행 시스템 구현

### 구현 계기

[DOMS 프로젝트](https://jkng-96.gitbook.io/devlog/home/3d)를 제작하는 당시 JWT 토큰을 사용하여 인증을 하기로 하였다. \
프론트엔드에서, JWT 토큰을 가지고 인증 절차를 구현하는 구조를 만들고 싶어서 프로젝트에 내가 구조를 구현한다고 하였다. 처음 해보는 작업이었지만 선임 [백엔드 개발자 (김락현)](https://github.com/LakHyeonKim) 와 함께 구조를 만들어나갔다.

우리가 떠올린 디자인 패턴은 프록시 패턴이었다. 프록시 패턴을 사용하면 요청을 가로채어 토큰을 추가하거나 검증할 수 있기 때문이다. 이 과정에서 우리는 [Axios](https://axios-http.com/kr/)의 [인터셉터](https://axios-http.com/kr/docs/interceptors)를 활용하여 프록시 패턴을 적용했다. 인터셉터를 사용하면 모든 HTTP 요청과 응답을 가로채고 처리할 수 있어 JWT 토큰을 이용한 인증 절차를 손쉽게 구현할 수 있다.

이 글에서 JWT 에 대한 기본적인 개념과 Axios 의 인터셉터를 활용하여 실제 프로젝트에 인증 및 재발행 시스템을 구현한 기술적인 내용을 다루고자 한다.\




### JWT 토큰이란

JSON Web Token (JWT) is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the **HMAC** algorithm) or a public/private key pair using **RSA** or **ECDSA**.

Although JWTs can be encrypted to also provide secrecy between parties, we will focus on _signed_ tokens. Signed tokens can verify the _integrity_ of the claims contained within it, while encrypted tokens _hide_ those claims from other parties. When tokens are signed using public/private key pairs, the signature also certifies that only the party holding the private key is the one that signed it.

공식문서의 정의를 한글로 요약하면,\
[JWT (Json Web Token)](https://jwt.io/introduction) 토큰은 정보의 안전한 전송을 위해 JSON 객체 형태로 정보를 담아 디지털 서명으로 신뢰성을 보장하는 개방형 표준이다. JWT는 HMAC 알고리즘을 사용한 비밀 키 또는 RSA나 ECDSA를 사용한 공개/개인 키 쌍으로 서명될 수 있으며, 서명된 토큰은 그 안의 정보 무결성을 검증할 수 있다.



#### JWT 의 구성 요소

JWT 는 3가지의 부분으로 구성되어있다.

1. 헤더 (Header)
2. 페이로드 (Payload)
3. 서명 (Signature)

이 세 부분은 각 Base64Url 로 인코딩되며, 점(.) 으로 구분된다.





### 헤더 (Header)

JWT 의 유형과 서명 알고리즘을 지정한다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```



### 페이로드 (Payload)

JWT 가 저장하는 클레임(Claim)을 포함한다. 클레임은 토큰에 담고자 하는 정보이며, \
보통 다음과 같은 세 가지 유형으로 나뉜다.

1.  등록된 클레임 (Registered claims): 등록된 클레임은 JWT 표준에 의해 정의된 사전 정해진 클레임으로, 특정 목적을 위해 사용된다. 이러한 클레임은 JWT의 상호 운용성을 보장하기 위해 정해진 것이다. 대표적인 등록된 클레임은 다음과 같다:\
    • iss (issuer): 토큰을 발급한 주체

    • sub (subject): 토큰의 주제나 사용자

    • aud (audience): 토큰의 수신자

    • exp (expiration): 토큰의 만료 시간

    • nbf (not before): 토큰이 유효해지기 시작하는 시간

    • iat (issued at): 토큰이 발급된 시간

    • jti (JWT ID): 토큰의 고유 식별자\

2.  공개 클레임 (Public claims) : 충돌을 방지하기 위해 IANA JSON Web Token Registry 또는 URI 형식을 통해 정의된 클레임\
    • name: 사용자 이름

    • email: 사용자 이메일 주소\

3.  비공개 클레임 (Private claims) : 공개적으로 사용되지 않으며, 클레임 이름과 의미가 해당 시스템 내에서만 이해되고 처리된다. 따라서 특정 애플리케이션이나 시스템 요구 사항에 맞춰 자유롭게 정의될 수 있다.\
    예를 들어,\
    • user\_id: 사용자 ID

    • role: 사용자 역할

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "iat": 1516239022
}
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







### AccessToken vs RefreshToken









### 요청과 응답을 가로채자







### AccessToken 재발급 받고 어떻게 다시 ajax 요청할까?





