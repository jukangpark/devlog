---
description: 왜 어떤 라이브러리는 Tree-Shaking 이 적용되지않는가?
cover: >-
  https://images.unsplash.com/photo-1502082553048-f009c37129b9?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwyfHxUcmVlLVNoYWtpbmd8ZW58MHx8fHwxNzIxMDExMzIzfDA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 모듈 시스템과 Tree-Shaking (작성중)

### 개요

Webpack Bundle Analyzer 로 측정해본 결과, [lodash](https://lodash.com), eCharts for React 등등 몇몇 라이브러리들이 Bundle 파일에 모두 포함되어있는 걸 발견하였다.\
\
Webpack 에서 build 시 [Tree Shaking](https://webpack.js.org/guides/tree-shaking/#root) 을 사용하도록 설정해주었는데, 어째서?!

<figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption><p>eCharts-for-react 모듈 모두 포함됨</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>lodash 모듈 모두 포함됨</p></figcaption></figure>



### Tree Shaking 이란?

[Tree Shaking](https://developer.mozilla.org/en-US/docs/Glossary/Tree\_shaking) 이란 최종 번들 결과물에서, 불필요한 코드가 없어지는 과정을 의미한다. MDN 의 정의를 살펴보자.

It relies on the [import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) and [export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) statements to detect if code modules are exported and imported for use between JavaScript files.

MDN 에서는 Tree Shaking 은 ES2015(ES6) 의 import / export 문에 의존하여 JavaScript 파일 간의 참조 여부를 판단한다고 설명되어 있다. Tree Shaking 은 ES6 부터 도입된 ES Modules(ESM)라고 불리는 모듈 시스템에 의존적이고, 많은 모듈 형식 중 ESM 형식이 Tree Shaking 을 가능하게 한다는 것을 의미한다.&#x20;

ESM 은 어떻게 Tree Shaking 을 가능하게 하는지, 다른 모듈 시스템과는 어떤 차이점이 있는지 알아보자.



### 모듈이란?

모듈이란 '재활용 가능한 코드 단위' 이다. 재활용 가능하다는 것은 어디에서 사용되더라도 동작의 일관성이 보장된다는 것이고, 코드 단위라는 것은 하나의 어플리케이션이 N 개의 모듈 집합으로 구성된다는 것을 의미한다.&#x20;



<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>

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



### AMD (Asynchronous Module Definition)

AMD는 브라우저 환경에서의 모듈 로딩 문제를 해결하기 위해 설계된 비동기 모듈 시스템이다. CommonJS는 서버 사이드 환경에 적합한 동기적 모듈 로딩을 제공하지만, 브라우저 환경에서는 메인 스레드가 모듈을 모두 불러올 때 까지 아무것도 할 수 없는 상태 (블로킹 문제)가 발생할 수 있다. AMD는 이러한 문제를 해결하기 위해 비동기 로딩과 명시적 의존성 관리를 제공하며, 이를 통해 브라우저 성능을 최적화할 수 있다. CommonJS 는 자바스크립트를 브라우저 밖으로 꺼내기 위해 탄생하였고, AMD 는 브라우저에 중점을 두고 있다. 'Asynchronous Module Definition' 이름에서 알 수 있듯이, AMD 는 비동기 모듈에 대한 표준안을 다루고 있다. 현대 웹 개발에서는 이러한 모듈 시스템의 장점을 결합한 ES6 모듈 시스템을 주로 사용하고 있다.



### UMD (Universal Module Definition)

AMD 와 CJS 진영이 나누어지다 보니 서로 호환되지 않는 문제가 발생했고, 이를 해결하기 위해 UMD 가 제안되었다. 브라우저, Node.js, 그리고 다른 JavaScript 런타임 환경에서도 모두 호환된다. UMD는 특히 라이브러리 제작에 유용하며, 다양한 환경에 대한 호환성을 제공하지만, 코드의 복잡성이 증가할 수 있다는 단점도 있다. 현대 JavaScript 개발에서는 주로 ES6 모듈 시스템을 사용하며, 번들링 도구를 통해 필요한 경우 UMD와 같은 호환성을 유지한다.



### ESM (ECMAScript Modules)

ESM(ECMAScript Modules)는 ECMAScript 2015(ES6) 표준에 도입된 JavaScript의 공식 모듈 시스템이다. ESM은 모듈을 쉽게 작성하고 사용하기 위한 구문과 기능을 제공한다. ESM은 최신 브라우저와 Node.js에서 기본적으로 지원되며, 이를 통해 JavaScript 모듈화를 더욱 간편하고 강력하게 만든다.



### ESM 의 동작 방식

ESM은 브라우저 또는 JavaScript 런타임 환경에서 모듈을 효율적으로 로드하고 실행하기 위해 여러 단계를 거친다. 여기서는 ESM의 모듈 로딩 과정과 종속성 트리 구성, 모듈 레코드 생성 등의 주요 단계를 설명하겠다.

ESM 시스템은

1. 시작점 설정 (Entry Point) : 모듈 로딩의 시작 파일을 명시한다.
2. 종속성 트리 구성 (Dependency Tree Construction) : import 문을 따라가며 종속성 트리를 생성한다.
3. 모듈 로드 (Module Loading) : 종속성 트리에 따라 필요한 모든 모듈 파일을 로드한다.
4. 구문 분석 (Parsing) : 로드된 파일을 파싱하여 import와 export 구문을 추출한다. 이 과정에서 모듈의 구조와 의존성을 파악한다.
5. 모듈 레코드 생성 (Module Record Creation) : 구문 분석을 통해 각 모듈의 import와 export 정보를 추출한 후, 이를 모듈 레코드(Module Record)라는 데이터 구조로 변환다. 모듈 레코드는 모듈의 메타데이터(이름, 경로, 종속성, 내보내기 정보 등)를 포함한다.
6. 종속성 해결 (Dependency Resolution) : 모듈 레코드를 기반으로 각 모듈의 종속성을 해결한다. 이를 통해 모듈 간의 연결 관계를 명확히 하여, 어떤 순서로 모듈을 실행해야 하는지 결정한다. 종속성 해결 과정에서 순환 종속성(circular dependency)도 처리한다.
7. 모듈 인스턴스화 (Module Instantiation) : 모듈을 실제로 사용할 수 있도록 인스턴스화한다. 이 과정에서는 모듈 레코드의 정보를 바탕으로 메모리에 모듈 객체를 생성하고, 필요한 경우 초기화를 수행한다. 인스턴스화된 모듈은 캐싱되어, 동일한 모듈이 다시 로드되지 않도록 한다.
8. 모듈 실행 (Module Execution) : 마지막으로, 인스턴스화된 모듈을 실행한다. 모듈의 코드가 실행되고, import된 값들이 바인딩된다. 이 과정은 종속성 트리의 최상위 모듈부터 하위 모듈로 순차적으로 이루어진다.



<figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption><p>Module Record</p></figcaption></figure>

import 로 연결된 파일 자체는 브라우저가 사용할 수 없으므로 Module Record(export, import 정보가 담긴 데이터) 구조로 변환해야 한다. 이 과정에서 모든 파일을 찾아 로드하고 모듈 레코드로 변환하기 위해 구문 분석을 수행한다.



### ESM 특징 : 정적 구조

CommonJS 와 달리 export 문은 ESM 의 최상위 레벨에만 위치할 수 있다. 컴파일러가 ESM 을 좀 더 쉽게 해석할 수 있게 하기 위한 제한이지만 메서드 호출 기반으로 api 를 동적으로 정의하고 export 해야만 하는 경우는 많지 않기 때문에 좋은 제한이기도 하다. ESM은 정적인 구조를 가지고 있다는 점이 가장 중요하다. 정적인 구조를 가졌기 때문에 빌드 타임에 모듈간 관계를 파악할 수 있고, 이를 기반으로 사용되지 않는 코드를 제거하는 작업도 가능하기 때문이다. 다음 글에서는  이 특징과 Tree Shaking 그리고, webpack 과 rollup 의 Tree Shaking 방식에 대해 정리해보겠다.



### Tree Shaking 과 ESM

<figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

Tree Shaking 은 기본적으로 모듈 구조를 정적으로 분석할 수 있는 ESM 에서 가능하다. CommonJS 에서는 runtime 에서 어떤 모듈을 로드할지 결정할 수 있기 때문에 빌드 단계에서 번들러가 어떤 모듈을 포함할지, 포함하지 않을지 쉽게 결정할 수 없다. 번들러마다 Tree Shaking 의 내부 원리는 조금씩 다를 수 있지만, '정적분석이 가능한 구조에 대해 더 잘 지원할 수 있다'는 사실은 동일하다.



### lodash 에 tree shaking 적용하기 <a href="#id-1-tree-shaking" id="id-1-tree-shaking"></a>

lodash 는 UMD module 로 export 하고 있기 때문에, Tree Shaking 이 제대로 적용되지 않는다. 이를 해결 하기 위해서는 webpack-common-shake 플러그인을 추가하는 방법과, import 시에 모듈에서 메서드까지 한 번에 import 하는 방법이 있었다.



```javascript
import debounce from "lodash/debounce"
```









