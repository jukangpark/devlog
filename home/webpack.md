---
description: 웹팩이란? 실무에서 UI 프로젝트 세팅 하기
cover: >-
  https://images.unsplash.com/photo-1541888946425-d81bb19240f5?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw0fHxlbmdpbmVlcnxlbnwwfHx8fDE3MjEwMDI3MzF8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Webpack 으로 프로젝트 초기 작업 하기 (작성중)

### 개요



### 웹팩이란?

<figure><img src="../.gitbook/assets/webpacklogo (1).svg" alt=""><figcaption><p>webpack</p></figcaption></figure>

[웹팩](https://webpack.js.org)은 JavaScript 모듈 번들러이다. 초기 js 는 크기가 작고 기능도 단순해서, '모듈'에 관한 표준 문법이 필요하지 않았는데, 코드를 모듈 단위로 구성해주는 방법 등 다양한 라이브러리를 만들었다. "AMD", "CommonJS", "UMD" 와 같은 모듈 시스템이 그 예이다. 모듈시스템에 대한 니즈가 커지면서 2015 년도에 표준 문법으로 등재되었다. 이제는 주요 브라우저와 Node.js 가 '모듈 시스템'을 지원한다. 브라우저 환경에서는 '모듈'을 단독으로 사용하기 보다, 번들링해서 배포서버에 올리는 방식을 주요 사용한다.



번들러란? js, css, 이미지 등의 파일을 묶어주는 작업을 '번들링(Bundling)' 이라고 하고, 작업의 결과물은 '번들(Bundle)' 이라고 한다. 웹팩 자체는 묶어주는 역할을 하기 때문에 '번들러(Bundler)' 라고 한다. 번들링 과정이 끝나면 기존 스크립트에서 import/export 가 사라지기 때문에 type="module"이 필요 없어진다. 따라서 번들링 과정을 거친 스크립트는 일반 스크립트 처럼 취급한다.\








