---
description: 소스 코드를 최소화 시키자!
cover: >-
  https://images.unsplash.com/photo-1612889845286-723dc4572b62?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw4fHx6aXB8ZW58MHx8fHwxNzIxMTA3Njc4fDA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 번들링 최적화 minify, uglify (작성중)

### 자바스크립트 압축과 난독화

이번에는 압축과 난독화의 개념과 장점에 대해 알아보고, 실무 적용 사례를 다뤄보고자 한다.





### minify

자바스크립트 파일의 크기를 줄여 웹페이지 로딩 속도를 개선하기 위한 방법이다. 이 과정에서 코드의 기능은 그대로 유지하면서 불필요한 부분을 제거하여 파일 크기를 최소화한다. Minification 의 주요 과정은 아래와 같다.

1. 공백 제거
2. 주석 제거
3. 변수 및 함수 이름 축약
4. 불필요한 코드 제거
5. 문자열 축약

예제)\
Minification 전 :&#x20;

```javascript
function addNumbers(a, b) {
    // 두 수를 더하는 함수
    var sum = a + b;
    return sum;
}

console.log(addNumbers(5, 10)); // 15 출력
```



Minification 후:

```javascript
function a(b,c){return b+c}console.log(a(5,10));
```



### Minification 의 장점

1. 파일 크기 감소
2. 성능 최적화
3. 코드 보호





### uglify





### 현재 프로젝트의 상황

실제 배포 환경에서 gzip 압축이 안되고 있었다.

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

그래서 우리는 [compression-webpack-plugin](https://webpack.kr/plugins/compression-webpack-plugin/) 을 사용하여 번들된 파일을 gzip 으로 압축하기로 하였다. 이 플러그인은 webpack 에 의해 관리 및 유지되지 않는 서드 파티 패키지 이지만,  npm Weekly Downloads 가 1,186,377 이나 되는 패키지이다. 관련된 내용을 보고 싶다면 [여기](https://www.npmjs.com/package/compression-webpack-plugin)를 참조 하길 바란다. 우선 패키지를 먼저 설치해준다.

```bash
npm install compression-webpack-plugin --save-dev
```







## compression-webpack-plugin

Prepare compressed versions of assets to serve them with Content-Encoding.&#x20;

