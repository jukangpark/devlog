---
description: 웹팩이란? 실무에서 UI 프로젝트 세팅 하기
cover: >-
  https://images.unsplash.com/photo-1541888946425-d81bb19240f5?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw0fHxlbmdpbmVlcnxlbnwwfHx8fDE3MjEwMDI3MzF8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Webpack 으로 프로젝트 초기 작업 하기 (작성중)

### 개요

입사한지 1년이 되었는데 새로운 [Builder R3](https://jkng-96.gitbook.io/devlog/home/undefined) 프로젝트의 프론트 담당자가 되었다.. 프로젝트를 구축해야하는데 무엇을 먼저해야되지? 웹팩 설정 작업!





### 웹팩이란?

<figure><img src="../.gitbook/assets/webpacklogo (1).svg" alt=""><figcaption><p>webpack</p></figcaption></figure>

[웹팩](https://webpack.js.org)은 JavaScript 모듈 번들러이다. 초기 js 는 크기가 작고 기능도 단순해서, '모듈'에 관한 표준 문법이 필요하지 않았는데, 코드를 모듈 단위로 구성해주는 방법 등 다양한 라이브러리를 만들었다. "AMD", "CommonJS", "UMD" 와 같은 모듈 시스템이 그 예이다. 모듈시스템에 대한 니즈가 커지면서 2015 년도에 표준 문법으로 등재되었다. 이제는 주요 브라우저와 Node.js 가 '모듈 시스템'을 지원한다. 브라우저 환경에서는 '모듈'을 단독으로 사용하기 보다, 번들링해서 배포서버에 올리는 방식을 주요 사용한다.



번들러란? js, css, 이미지 등의 파일을 묶어주는 작업을 '번들링(Bundling)' 이라고 하고, 작업의 결과물은 '번들(Bundle)' 이라고 한다. 웹팩 자체는 묶어주는 역할을 하기 때문에 '번들러(Bundler)' 라고 한다. 번들링 과정이 끝나면 기존 스크립트에서 import/export 가 사라지기 때문에 type="module"이 필요 없어진다. 따라서 번들링 과정을 거친 스크립트는 일반 스크립트 처럼 취급한다.\




### 웹팩을 사용하는 이유

1. 웹팩은 여러개의 파일을 하나로 번들링 하기 때문에 HTTP 요청 횟수를 줄일 수 있다. (http 요청 횟수 감소) -> 빠른 서비스&#x20;
2. 또한, 자바스크립트 외의 리소스 포맷의 모듈도 사용할 수 있게 해준다. \
   CSS 든, 이미지든 사용하려는 곳에 해당 리소스를 import 해주기만 하면 웹팩이 알아서 빌드해줌.&#x20;
3. 웹팩이 알아서 자동화 해주는 덕분에, 코드를 수정했을 때, 다시 빌드하고 새로고침하지 않아도, \
   바로바로 빌드 결과를 확인할 수 있다.&#x20;
4. 코드 스플리팅으로 원하는 Require.js 와 같은 라이브러리 없이도 통째로 로딩하지 않고, \
   필요한 순간에 원하는 모듈을 불러올 수 있다고 한다. (Dynamic Loading, Lazy Loading)
5. 실제 사용되는 함수가 무엇인지 파악해, \
   그렇지 않은 함수는 최종 번들링 결과물에 포함하지 않는다.\
   이 과정에서 불필요한 코드가 제거되기 때문에 빌드 결과물의 크기가 작아진다.\
   이런 최적화 과정을 '가지치기(tree-shaking)'라고 부른다.





### Concepts

1. [**Entry**](https://webpack.js.org/configuration/entry-context/#entry)\
   config파일에서 entry 속성을 설정해서 웹팩이 어떤 모듈로부터 시작해서, \
   디펜던시 그래프를 그려나갈지 명시해줄 수 있다.\
   'entry' 속성의 기본값은 './src/index.js'이지만 \
   다른 Entry Point를 지정할 수도 있다. (여러 개도 지정 가능)\

2. [**Output**](https://webpack.js.org/concepts/output/#root)\
   웹팩이 번들을 꾸리고 나서 결과물을 어디로 내보낼지 지정하는 속성이다. \

3. [**Loaders**](https://webpack.js.org/loaders/)\
   이제까지 자바스크립트 외의 리소스도 번들링할 수 있다고 했지만, \
   사실 웹팩은 기본적으로 JavaScript와 JSON 파일만 이해할 수 있다. \
   이 때 필요한 것이 Loader이다. \
   사용하려는 포맷에 대응하는 Loader를 설정해주면 다른 포맷의 리소스도 디펜던시 그래프에 추가할 수있게 된다.\

4. [**Plugin**](https://webpack.js.org/concepts/plugins/#root)\


