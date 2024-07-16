---
description: 왜 어떤 라이브러리는 Tree-Shaking 이 적용되지않는가?
cover: >-
  https://images.unsplash.com/photo-1502082553048-f009c37129b9?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwyfHxUcmVlLVNoYWtpbmd8ZW58MHx8fHwxNzIxMDExMzIzfDA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 모듈 시스템과 Tree-Shaking

### 개요

Webpack Bundle Analyzer 로 측정해본 결과, [lodash](https://lodash.com), eCharts for React 등등 몇몇 라이브러리들이 Bundle 파일에 모두 포함되어있는 걸 발견하였다.\
\
Webpack 에서 build 시 [Tree Shaking](https://webpack.js.org/guides/tree-shaking/#root) 을 사용하도록 설정해주었는데, 어째서?!

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption><p>eCharts-for-react 모듈 모두 포함됨</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1).png" alt=""><figcaption><p>lodash 모듈 모두 포함됨</p></figcaption></figure>



### Tree Shaking 이란?

[Tree Shaking](https://developer.mozilla.org/en-US/docs/Glossary/Tree\_shaking) 이란 최종 번들 결과물에서, 불필요한 코드가 없어지는 과정을 의미한다. MDN 의 정의를 살펴보자.

It relies on the [import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) and [export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) statements to detect if code modules are exported and imported for use between JavaScript files.

MDN 에서는 Tree Shaking 은 ES2015(ES6) 의 import / export 문에 의존하여 JavaScript 파일 간의 참조 여부를 판단한다고 설명되어 있다. Tree Shaking 은 ES6 부터 도입된 ES Modules(ESM)라고 불리는 모듈 시스템에 의존적이고, 많은 모듈 형식 중 ESM 형식이 Tree Shaking 을 가능하게 한다는 것을 의미한다.&#x20;

ESM 은 어떻게 Tree Shaking 을 가능하게 하는지, 다른 모듈 시스템과는 어떤 차이점이 있는지 알아보자.



### 모듈이란?

모듈이란 '재활용 가능한 코드 단위' 이다. 재활용 가능하다는 것은 어디에서 사용되더라도 동작의 일관성이 보장된다는 것이고, 코드 단위라는 것은 하나의 어플리케이션이 N 개의 모듈 집합으로 구성된다는 것을 의미한다.&#x20;



<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

JavaScript 에 모듈 개념이 도입 되기 이전에 공통 변수인 foo 를 foo.js 와 bar.js 가 공유하는 가장 쉬운 방법은 foo 를 전역변수로 끌어올리는 것이었다. 이 방법은 전역 변수의 상태나 할당시점을 제어할 수 없기 때문에 js 로드 순서에 의존적이고 변수 참조에 대한 의존성 관리가 어려워진다. javaScript 에 모듈 시스템이 도입되기 시작하였고, 클라이언트 / 서버 사이드로 나누어져 제안된 것이 [CommonJS](https://wiki.commonjs.org/wiki/CommonJS) 와 [AMD(Asychronous Module Definition)](https://github.com/amdjs/amdjs-api/wiki/AMD) 이다.

javaScript 모듈 시스템은 크게 CommonJS 와 AMD 진영으로 나뉘게 되었고, 브라우저에서 모듈을 사용하기 위해서는 CommonJS 혹은 AMD 를 구현한 모듈 로더 라이브러리를 사용해야 하는 상황이 되었다.

Node.js 환경에서는 CommonJS 를 채택하였다. CommonJS 의 특징은 아래와 같다. [(참고 : Node.js 17 버전에서는 ECMASCript module 지원이 추가되었다.)](https://nodejs.org/api/esm.html#modules-ecmascript-modules)

1. 동기적 모듈 로딩 (Synchronous Module Loading)\
   동기적 모듈 로딩은 require 함수가 호출될 때, 해당 모듈을 즉시 로드하고 실행하는 방식이다. 이 과정은 호출된 순서대로 진행되며, 모든 로드가 완료될 때까지 다음 코드가 실행되지 않는다. 이 방식은 서버 환경에서 매우 유용하다. 서버 환경에서는 모든 모듈이 로컬 파일 시스템에 존재하고, 파일 시스템 접근 속도가 빠르기 때문에 모듈을 동기적으로 로드해도 성능 저하가 크지 않다.
2. 정적 바인딩 (Static Binding)\
   정적 바인딩은 모듈의 의존성이 파일이 로드되는 시점에 고정된다는 것을 의미한다. CommonJS 모듈은 코드 실행 중에 모듈을 동적으로 변경하거나 재평가하지 않는다. 모듈이 처음 로드 될 때 require 가 호출되고, 해당 모듈이 메모리에 로드되어 module.exports 객체에 할당된다. 이후에는 동일한 모듈을 다시 require 하면, 캐싱된 객체를 반환한다.

장점

1\. 간단하고 명확함: 동기적 로딩과 정적 바인딩은 코드의 실행 흐름을 이해하기 쉽게 합니다.\
2\. 빠른 로드 시간: 서버 환경에서 모든 모듈이 로컬에 존재하기 때문에, 동기적 로딩이 성능에 큰 영향을 미치지 않습니다.\
3\. 의존성 관리 용이: 의존성이 정적으로 결정되기 때문에, 모듈 간의 의존성 관계를 쉽게 추적하고 관리할 수 있습니다.\


단점

1\. 브라우저 환경에서 비효율적: 동기적 로딩은 네트워크 지연으로 인해 브라우저 환경에서 비효율적일 수 있습니다. 브라우저는 비동기적으로 스크립트를 로드하는 것이 더 적합합니다.\
2\. 동적 의존성 관리 어려움: 모듈을 동적으로 로드하거나 변경할 수 없기 때문에, 동적 의존성 관리를 하기 어렵습니다다.



\








### JavaScript의 모듈 시스템



1. CJS (CommonJS)
2. AMD (Asynchronous Module Definition)
3. UMD (Universal Module Definition)
4. ESM





