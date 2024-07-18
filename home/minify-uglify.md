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



### Terser Plugin vs Gzip 압축

1. **Terser Plugin**:
   * **기능**: Terser plugin은 웹팩(Webpack)을 통해 JavaScript 코드를 압축하고 난독화하는 도구이다.
   * **작동 방식**: JavaScript 코드를 분석하여 코드에서 불필요한 부분을 제거하고, 변수와 함수 이름을 짧게 만들어 번들 크기를 줄인다. 이는 주로 개발자가 작성한 코드의 크기를 최소화하고, 브라우저에서 실행될 때의 성능을 최적화하는 데 사용된다.
   * **적용 대상**: 주로 JavaScript 파일에 대해 적용되며, 코드의 가독성을 낮추고 압축된 코드를 생성한다.
2. **Gzip 압축**:
   * **기능**: Gzip 압축은 전송 시 데이터를 압축하여 파일 크기를 줄이는 기술이다.
   * **작동 방식**: 서버는 클라이언트에게 파일을 전송하기 전에 Gzip 알고리즘을 사용하여 파일을 압축합니다. 클라이언트 브라우저는 이를 받아서 압축을 해제하고 사용한다.
   * **적용 대상**: 모든 종류의 파일에 적용 가능하지만, 주로 텍스트 기반 파일(HTML, CSS, JavaScript)에 사용되며, 이미지나 비디오와 같은 바이너리 파일에는 사용되지 않는다.

**차이점 요약**:

* Terser plugin은 코드를 압축하고 난독화하여 번들 크기를 줄이고 실행 속도를 개선하는 데 사용된다.
* Gzip 압축은 파일을 네트워크를 통해 전송할 때 파일 크기를 줄이는 데 사용되며, 파일의 원본 크기와는 독립적으로 작동한다.

일반적으로, 개발 단계에서는 Terser plugin을 사용하여 웹팩으로 JavaScript 코드를 최적화하고, 운영 환경에서는 웹 서버 설정에서 Gzip 압축을 활성화하여 네트워크 전송 시 파일 크기를 최소화하는 것이 좋다.



### uglify



### Terser Plugin 을 적용해보자!



### Compression webpack plugin 을 적용해보자!





### 현재 프로젝트의 상황

실제 배포 환경에서 gzip 압축이 안되고 있었다.

<figure><img src="../.gitbook/assets/image (35).png" alt=""><figcaption></figcaption></figure>

그래서 우리는 [compression-webpack-plugin](https://webpack.kr/plugins/compression-webpack-plugin/) 을 사용하여 번들된 파일을 gzip 으로 압축하기로 하였다. 이 플러그인은 webpack 에 의해 관리 및 유지되지 않는 서드 파티 패키지 이지만,  npm Weekly Downloads 가 1,186,377 이나 되는 패키지이다. 관련된 내용을 보고 싶다면 [여기](https://www.npmjs.com/package/compression-webpack-plugin)를 참조 하길 바란다. 우선 패키지를 먼저 설치해준다.

```bash
npm install compression-webpack-plugin --save-dev
```







## compression-webpack-plugin

Prepare compressed versions of assets to serve them with Content-Encoding.&#x20;

