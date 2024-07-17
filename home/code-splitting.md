---
description: webpack 에서 Code Splitting 을 활용하여 번들링 최적화를 해보자!
cover: >-
  https://images.unsplash.com/photo-1580674285054-bed31e145f59?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxfHxwYWNrYWdlfGVufDB8fHx8MTcyMTAwMTc1N3ww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 번들링 최적화 Code Splitting (작성중)

### 개요

[SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA) 로 구성된 웹앱의 경우 Performance를 보면 초기에 번들 파일(js)을 다운 받기 때문에 시간이 오래 걸린다. 그리고 이 큰 번들 파일(js)을 클라이언트 측에서 실행되기 위해 초기화 과정을 거치게 되는데, 이 과정에서 DOM 조작, 이벤트 리스너 설정, 상태 초기화 등의 작업이 실행된다. Builder R3 프로젝트는 코드 스플리팅과 Lazy Loading 이 적용되지 않았기 때문에, 모든 자바스크립트 코드가 초기 로딩 시점에 한꺼번에 다운로드 되고, 실행되므로 초기 로딩 시간이 길어졌다.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption><p>Builder R3 의 큰 사이즈의 번들 파일(26.16 MB)</p></figcaption></figure>

Webpack Bundle Analyzer 를 사용하여 번들 파일을 분석한 결과, Stat Size 기준으로 26.16MB 가 측정되었다.

1. Stat Size : Webpack 이 각 모듈을 번들에 포함하기 전에 측정한 원본 크기
2. Parsed Size : 브라우저가 번들 파일을 다운로드한 후에 JavaScript 엔진이 실제로 파싱한 후의 크기. 이는 번들 파일이 실행 가능한 코드로 변환된 후의 크기
3. Gzipped Size : 번들 파일이 Gzip 압축을 통해 압축된 후의 크기. Gzip 압축은 네트워크를 통해 전송될 때 데이터 크기를 줄여주는 역할을 한다.\
   \
   \
   번들링 최적화 하기 위해 다음과 같은 기법들을 적용할 수 있다.
4. 코드 스플리팅 : Webpack 과 같은 번들러를 사용하여 코드를 여러 개의 청크로 나누어 필요한 시점에 로드한다.
5. Lazy Loading : 사용자가 필요로 할 때에만 리소스를 로드한다.
6. 서버 측 렌더링 (SSR) : 초기 페이지를 서버 측에서 렌더링하여 초기 로딩 시간을 줄인다.
7. 미니파이와 압축 : JavaScript 파일을 미니파이하고 Gzip 압축을 사용하여 파일 크기를 줄인다.
8. 캐싱 : 브라우저 캐싱을 활용하여 동일한 리소스를 반복적으로 다운로드하지 않도록 한다.
9. 트리 쉐이킹 : 사용하지 않는 코드를 제거하여 번들 크기를 줄인다.
10. 적절한 로딩 순서 설정 : 중요한 CSS 와 JavaScript를 먼저 로드하고 덜 중요한 리소스는 나중에 로드한다.

이번 글에서는 실무에서 코드 스플리팅과 Lazy Loading 을 활용하여 최적화 한 내용에 대하여 다루고자 한다.



### Code Splitting

[코드 스플리팅](https://webpack.js.org/guides/code-splitting/)이란 웹팩의 가장 강력한 기능 중 하나이다. 이 기능을 사용하면 코드를 다양한 번들로 분할한 다음 필요에 따라 또는 병렬로 로드할 수 있다. 더 작은 번들을 달성하고 리소스 로드 우선 순위를 제어하는 데 사용할 수 있으며 올바르게 사용하면 로드 시간에 큰 영향을 미칠 수 있다. 코드 스플리팅에는 일반적으로 3가지 접근 방법이 있다.\
\
Code splitting is one of the most compelling features of webpack. This feature allows you to split your code into various bundles which can then be loaded on demand or in parallel. It can be used to achieve smaller bundles and control resource load prioritization which, if used correctly, can have a major impact on load time.

There are three general approaches to code splitting available:

* **Entry Points**: Manually split code using [`entry`](https://webpack.js.org/configuration/entry-context) configuration.
* **Prevent Duplication**: Use [Entry dependencies](https://webpack.js.org/configuration/entry-context/#dependencies) or [`SplitChunksPlugin`](https://webpack.js.org/plugins/split-chunks-plugin/) to dedupe and split chunks.
* [**Dynamic Imports**](https://webpack.js.org/guides/code-splitting/#dynamic-imports): Split code via inline function calls within modules.

위의 3가지 접근 방법 중, 우리는 [Prevent Duplication](https://webpack.kr/guides/code-splitting/#prevent-duplication) 과 [Dynamic Imports](https://webpack.kr/guides/code-splitting/#dynamic-imports)  를 사용하기로 하였다. [SplitChunksPlugin](https://webpack.kr/guides/code-splitting/#splitchunksplugin) 으로 중복 코드를 제거하고, Dynamic Imports 를 통해 모듈을 비동기로 로드하고, Code Splitting 을 통해 애플리케이션을 여러 개의 작은 청크로 분리하여 초기 로딩 시간을 단축하고자 한다.&#x20;



### SplitChunksPlugin

[`SplitChunksPlugin`](https://webpack.kr/plugins/split-chunks-plugin/)을 사용하면 기존 엔트리 청크 또는 완전히 새로운 청크로 공통 의존성을 추출할 수 있다. [`optimization.splitChunks`](https://webpack.kr/plugins/split-chunks-plugin/#optimizationsplitchunks) 설정 옵션을 적용하면 각 모듈에서 중복 의존성이 제거된 것을 확인 할 수 있다.\
Builder R3 프로젝트에서  SplitChunksPlugin 을 사용하여, **Prevent Duplication** 한 사례를 소개하고자 한다.





