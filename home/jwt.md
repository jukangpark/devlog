---
description: 어떻게 해야 토큰을 재발행 받고 ajax 재요청 할 수 있는 구조를 프론트에서 구현할 수 있을까?
cover: >-
  https://images.unsplash.com/photo-1639152201720-5e536d254d81?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw3fHx0b2tlbnxlbnwwfHx8fDE3MjA1OTQwNDB8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# JWT 토큰을 활용한 프론트엔드 인증 및 재발행 시스템 구현

### 구현 계기

[DOMS 프로젝트](https://jkng-96.gitbook.io/devlog/home/3d)를 제작하는 당시 JWT 토큰을 사용하여 인증을 하기로 하였다. 프론트엔드에서, JWT 토큰을 가지고 인증 절차를 구현하는 구조를 만들고 싶어서 프로젝트에 내가 구조를 구현한다고 하였다. 처음 해보는 작업이었지만 선임 [백엔드 개발자 (김락현)](https://github.com/LakHyeonKim) 와 함께 구조를 만들어나갔다.

우리가 떠올린 디자인 패턴은 프록시 패턴이었다. 프록시 패턴을 사용하면 요청을 가로채어 토큰을 추가하거나 검증할 수 있기 때문이다. 이 과정에서 우리는 [Axios](https://axios-http.com/kr/)의 [인터셉터](https://axios-http.com/kr/docs/interceptors)를 활용하여 프록시 패턴을 적용했다. 인터셉터를 사용하면 모든 HTTP 요청과 응답을 가로채고 처리할 수 있어 JWT 토큰을 이용한 인증 절차를 손쉽게 구현할 수 있다.

이 글에서 JWT 에 대한 기본적인 개념과 Axios 의 인터셉터를 활용하여 실제 프로젝트에 인증 및 재발행 시스템을 구현한 기술적인 내용을 다루고자 한다.\




### JWT 토큰이란

JSON Web Token (JWT) is an open standard ([RFC 7519](https://tools.ietf.org/html/rfc7519)) that defines a compact and self-contained way for securely transmitting information between parties as a JSON object. This information can be verified and trusted because it is digitally signed. JWTs can be signed using a secret (with the **HMAC** algorithm) or a public/private key pair using **RSA** or **ECDSA**.

Although JWTs can be encrypted to also provide secrecy between parties, we will focus on _signed_ tokens. Signed tokens can verify the _integrity_ of the claims contained within it, while encrypted tokens _hide_ those claims from other parties. When tokens are signed using public/private key pairs, the signature also certifies that only the party holding the private key is the one that signed it.

공식문서의 정의를 한글로 요약하면,\
[JWT (Json Web Token)](https://jwt.io/introduction) 토큰은 정보의 안전한 전송을 위해 JSON 객체 형태로 정보를 담아 디지털 서명으로 신뢰성을 보장하는 개방형 표준이다. JWT는 [HMAC](https://ko.wikipedia.org/wiki/HMAC) 알고리즘을 사용한 비밀 키 또는 [RSA](https://ko.wikipedia.org/wiki/RSA\_%EC%95%94%ED%98%B8)나 [ECDSA](https://ko.wikipedia.org/wiki/%ED%83%80%EC%9B%90%EA%B3%A1%EC%84%A0\_DSA)를 사용한 공개/개인 키 쌍으로 서명될 수 있으며, 서명된 토큰은 그 안의 정보 무결성을 검증할 수 있다.



#### JWT 의 구성 요소

JWT 는 3가지의 부분으로 구성되어있다.

1. 헤더 (Header)
2. 페이로드 (Payload)
3. 서명 (Signature)

이 세 부분은 각 Base64Url 로 인코딩되며, 점(.) 으로 구분된다.

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>



### 헤더 (Header)

헤더는 토큰의 타입(JWT)과 서명 알고리즘(HMAC SHA256 or RSA)으로 구성된다. 여기서 alg 는 헤더를 암호화 하는 것이 아닌, Signature 를 해싱하기 위한 알고리즘을 지정하는 것이다.

```json
{
  "alg": "HS256", // HS256(SHA256) 또는 RSA
  "typ": "JWT" // 토큰의 타입을 지정
}
```



### 페이로드 (Payload)

전달하는 정보들을 담는 곳이다. 그리고 이 정보들을 Claim 이라 부른다. payload 에 있는 정보들은 수정이 가능하여 더 많은 정보를 추가할 수 있다. 그러나 노출과 수정이 가능하기 때문에 필요한 최소한의 정보만을 담아야한다. 클레임은 보통 다음과 같은 세 가지 유형으로 나뉜다.

1.  등록된 클레임 (Registered claims): JWT 표준에 의해 이미 정의된 클레임이다. JWT 를 간결하게 하기 위해 key 는 모두 길이 3의 String 이다.\
    • iss (issuer): 토큰 발급자

    • sub (subject): 토큰 제목

    • aud (audience): 토큰 대상자

    • exp (expiration): 토큰 만료 시간 (NumericDate 형식) 1480849147370

    • nbf (not before): 토큰 활성 날짜, 이 날이 지나기 전의 토큰은 활성화 되지 않음

    • iat (issued at): 토큰 발급 시간, 토큰 발급 이후의 경과 시간을 알 수 있음

    • jti (JWT ID): 토큰 고유 식별자, 중복 방지를 위해 사용하며, 일회용 토큰 (AccesToken) 등에 사용\

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
  "admin": true
}
```



### 서명 (Signature)

서명은 토큰을 인코딩하거나 유효성 검사를 할 때 사용하는 고유한 암호화 코드이다. 서명 (Signature) 은 위에서 만든 헤더와 페이로드의 값을 각각 Base64Url 로 인코딩하고, 인코딩한 값을 비밀키를 이용해 헤더에서 정의한 알고리즘으로 해싱을 하고, 이 값은 다시 Base64Url 로 인코딩 하여 생성한다.



예를 들어 HMAC SHA256 알고리즘을 사용하려는 경우 다음과 같은 방법으로 서명이 생성된다.

```
HMACSHA256(
  base64UrlEncode(header) + "." +
  base64UrlEncode(payload),
  secret)
```

서명은 메시지가 도중에 변경되지 않았는지 확인하는 데 사용되며, 개인 키로 서명된 토큰의 경우 JWT의 발신자가 누구인지 확인할 수도 있다.



<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>

JWT 를 가지고 연습하려면 [jwt.io.Debbugger](https://jwt.io/#debugger-io) 를 decode, verify 그리고 JWT 를 생성할 때 사용하면 된다.





### JWT 장점과 단점

#### 장점

사용자 인증에 필요한 모든 정보는 토큰 자체에 포함하기 때문에 별도의 인증 저장소가 필요없다는 것이다. 분산 마이크로 서비스 환경에서 중앙 집중식 인증 서버와 데이터베이스에 의존하지 않는 쉬운 인증 및 인가 방법을 제공한다. 개별 마이크로 서비스에는 토큰 검증과 검증에 필요한 비밀 키를 처리하기위한 미들웨어가 필요하다. 검증은 서명 및 클레임과 같은 몇 가지 매개 변수를 검사하는 것과 토큰이 만료되는 경우로 구성된다. JSON 웹 토큰의 사용을 권장하는 몇 가지 이유는 다음과 같다.



1. URL 파라미터와 헤더로 사용
2. 수평 스케일이 용이
3. 디버깅 및 관리가 용이
4. 트래픽 대한 부담이 낮음
5. REST 서비스로 제공 가능
6. 내장된 만료
7. 독립적인 JWT



**단점**

1. 토큰은 클라이언트에 저장되어 데이터베이스에서 사용자 정보를 조작하더라도 토큰에 직접 적용할 수 없다.
2. 더 많은 필드가 추가되면 토큰이 커질 수 있다.
3. 비상태 애플리케이션에서 토큰은 거의 모든 요청에 대해 전송되므로 데이터 트래픽 크기에 영향을 미칠 수 있다.



### JWT 를 어떻게 사용해요?

HTTP 헤더를 통해 JWT 를 보내 인증을 수행하는 경우 아래와 같이 사용한다.

```
Authorization: Bearer <token>
```

HTTP 헤더를 통해 JWT 토큰을 보내는 경우 너무 커지는 것을 방지해야 한다. 일부 서버는 헤더에 8KB 이상을 허용하지 않는다. 사용자의 모든 권한을 포함하는 것처럼 JWT 토큰에 너무 많은 정보를 포함하려고 하면 Auth0 Fine-Grained Authorization과 같은 대체 솔루션이 필요할 수 있다.

<figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption><p>인증 과정</p></figcaption></figure>

위와 같은 형태로 클라이언트에서 인증 구조를 구현하려고 했다. 그러면 여기서 잠깐 Access Token 과 Refresh Token 차이가 무엇일까?



### AccessToken vs RefreshToken

#### Access Token:

• 목적: 리소스 접근 권한 증명\
• 수명: 짧음 (몇 분에서 몇 시간)\
• 사용 방식: 요청 시 토큰 전송\
• 보안 측면: 짧은 수명으로 보안성 향상

#### Refresh Token:

• 목적: 새로운 Access Token 발급\
• 수명: 김 (몇 주에서 몇 달)\
• 사용 방식: Access Token 만료 시 갱신 요청\
• 보안 측면: 안전한 저장 필요, 높은 보안 기준 요구



요약 : 이 2개의 토큰을 사용하여 보안과 사용자  경험 모두 최적화 할 수 있다. \
Access Token 을 짧게 유지하여 보안을 강화하고, \
Refresh Token 을 통해 사용자 로그인 빈도를 줄여 편의성을 높일 수 있다.



### 요청과 응답을 가로채자

그럼 이제 기본적인 개념들과 설계 방향성에 대하여 모두 알았으니, 실제로 Axios 의 [인터셉터](https://axios-http.com/kr/docs/interceptors)를 사용하여 요청와 응답을 가로채보자. 공식문서를 보면 then 또는 catch 로 처리되기 전에 요청과 응답을 가로챌 수 있다고 되어있다.\
Promise 와 관련된 배경지식이 부족하다면 [여기](https://www.instagram.com/p/C3PFvcYSmO\_/?img\_index=1)를 보고 오자.

```javascript
// 요청 인터셉터 추가하기
axios.interceptors.request.use(function (config) {
    // 요청이 전달되기 전에 작업 수행
    return config;
  }, function (error) {
    // 요청 오류가 있는 작업 수행
    return Promise.reject(error);
  });

// 응답 인터셉터 추가하기
axios.interceptors.response.use(function (response) {
    // 2xx 범위에 있는 상태 코드는 이 함수를 트리거 합니다.
    // 응답 데이터가 있는 작업 수행
    return response;
  }, function (error) {
    // 2xx 외의 범위에 있는 상태 코드는 이 함수를 트리거 합니다.
    // 응답 오류가 있는 작업 수행
    return Promise.reject(error);
  });
```



우리는 Axios 커스텀 [인스턴스](https://axios-http.com/kr/docs/instance)를 사용하고 있었기 때문에, \
아래와 같이 구현해야만 했다.

```javascript
const instance = axios.create();
instance.interceptors.request.use(function () {/*...*/});
```

예제 코드를 보면 알 수 있듯이,&#x20;





Access Token 이 만료가 되었을 때 errorCode 는 5001 이고\
만약 Access Token 이 유효하지 않은 경우의 errorCode 는 \




### AccessToken 재발급 받고 어떻게 다시 ajax 요청할까?



다시 ajax 요청을 하기 위해서는 어떤 구조로 접근해야할까? 고민했었다.



