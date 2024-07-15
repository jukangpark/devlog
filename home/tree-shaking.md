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

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>lodash 모듈 모두 포함됨</p></figcaption></figure>



### Tree Shaking 이란?

Tree Shaking 은 사용되지 않는 코드를 제거하기 위해 JavaScript 컨텍스트에서 일반적으로 사용되는 용어이다. ES2015 모듈 구문은 [정적 구조](http://exploringjs.com/es6/ch\_modules.html#static-module-structure)에 의존한다. 예를 들면, [`import`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)와 [`export`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)가 있다. 이름과 개념은 ES2015 모듈 번들러의 [rollup](https://github.com/rollup/rollup)에 의해 대중화되었다.

webpack 2 릴리스에서는 ES2015 모듈(별칭 _harmony 모듈_)과 사용하지 않는 모듈의 export를 감지하는 기능을 제공한다. 새로운 webpack 4의 릴리스는 `package.json`의 `"sideEffects"` 프로퍼티를 통해 컴파일러에 힌트를 제공하는 방식으로 기능을 확장한다. 프로젝트의 어떤 파일이 "순수"한지 나타내며, 만약 사용하지 않는다면 제거해도 괜찮은지 표시한다.



### 모듈 시스템

Tree Shaking 이 안된 이유는 해당 라이브러리에서 사용하고 있는 모듈 시스템 때문이었다.&#x20;

