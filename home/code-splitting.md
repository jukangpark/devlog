---
description: webpack 에서 Code Splitting 을 활용하여 번들링 최적화를 해보자!
cover: >-
  https://images.unsplash.com/photo-1580674285054-bed31e145f59?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxfHxwYWNrYWdlfGVufDB8fHx8MTcyMTAwMTc1N3ww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 번들링 최적화 Code Splitting (작성중)

### 개요

[SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA) 로 구성된 웹앱의 경우 Performance를 보면 초기에 번들 파일(js)을 다운 받기 때문에 시간이 오래 걸린다. 그리고 이 큰 번들 파일(js)을 클라이언트 측에서 실행되기 위해 초기화 과정을 거치게 되는데, 이 과정에서 DOM 조작, 이벤트 리스너 설정, 상태 초기화 등의 작업이 실행된다. Builder R3 프로젝트는 코드 스플리팅과 Lazy Loading 이 적용되지 않았기 때문에, 모든 자바스크립트 코드가 초기 로딩 시점에 한꺼번에 다운로드 되고, 실행되므로 초기 로딩 시간이 길어졌다.

<figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption><p>Builder R3 의 큰 사이즈의 번들 bundle.main.js (26.16 MB)</p></figcaption></figure>

Webpack Bundle Analyzer 를 사용하여 번들 파일을 분석한 결과, Stat Size 기준으로 26.16MB 가 측정되었다.

1. Stat Size : Webpack 이 각 모듈을 번들에 포함하기 전에 측정한 원본 크기
2. Parsed Size : 브라우저가 번들 파일을 다운로드한 후에 JavaScript 엔진이 실제로 파싱한 후의 크기. 이는 번들 파일이 실행 가능한 코드로 변환된 후의 크기
3. Gzipped Size : 번들 파일이 Gzip 압축을 통해 압축된 후의 크기. Gzip 압축은 네트워크를 통해 전송될 때 데이터 크기를 줄여주는 역할을 한다.

### 번들링 최적화 기법들 <a href="#bundle-analyzer" id="bundle-analyzer"></a>

1. 코드 스플리팅 : Webpack 과 같은 번들러를 사용하여 코드를 여러 개의 청크로 나누어 필요한 시점에 로드한다.
2. Lazy Loading : 사용자가 필요로 할 때에만 리소스를 로드한다.
3. 서버 측 렌더링 (SSR) : 초기 페이지를 서버 측에서 렌더링하여 초기 로딩 시간을 줄인다.
4. 미니파이와 압축 : JavaScript 파일을 미니파이하고 Gzip 압축을 사용하여 파일 크기를 줄인다.
5. 캐싱 : 브라우저 캐싱을 활용하여 동일한 리소스를 반복적으로 다운로드하지 않도록 한다.
6. 트리 쉐이킹 : 사용하지 않는 코드를 제거하여 번들 크기를 줄인다.
7. 적절한 로딩 순서 설정 : 중요한 CSS 와 JavaScript를 먼저 로드하고 덜 중요한 리소스는 나중에 로드한다.

이번 글에서는 실무에서 코드 스플리팅과 Lazy Loading 을 활용하여 최적화 한 내용에 대하여 다루고자 한다.



### Code Splitting

Code splitting is one of the most compelling features of webpack. This feature allows you to split your code into various bundles which can then be loaded on demand or in parallel. It can be used to achieve smaller bundles and control resource load prioritization which, if used correctly, can have a major impact on load time.

[코드 스플리팅](https://webpack.js.org/guides/code-splitting/)이란 웹팩의 가장 강력한 기능 중 하나이다. 이 기능을 사용하면 코드를 다양한 번들로 분할한 다음 필요에 따라 또는 병렬로 로드할 수 있다. 더 작은 번들을 달성하고 리소스 로드 우선 순위를 제어하는 데 사용할 수 있으며 올바르게 사용하면 로드 시간에 큰 영향을 미칠 수 있다. 코드 스플리팅에는 일반적으로 3가지 접근 방법이 있다.

* **Entry Points**: Manually split code using [`entry`](https://webpack.js.org/configuration/entry-context) configuration.
* **Prevent Duplication**: Use [Entry dependencies](https://webpack.js.org/configuration/entry-context/#dependencies) or [`SplitChunksPlugin`](https://webpack.js.org/plugins/split-chunks-plugin/) to dedupe and split chunks.
* [**Dynamic Imports**](https://webpack.js.org/guides/code-splitting/#dynamic-imports): Split code via inline function calls within modules.

위의 3가지 접근 방법 중, 우리는 [SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/) 과 [Dynamic Imports](https://webpack.kr/guides/code-splitting/#dynamic-imports)  를 사용하기로 하였다. [SplitChunksPlugin](https://webpack.kr/guides/code-splitting/#splitchunksplugin) 으로 node\_modules 폴더에 있는 외부 라이브러리를 별도의 청크로 분리하고, Dynamic Imports 를 통해 모듈을 비동기로 로드하고, 애플리케이션을 여러 개의 작은 청크로 분리하여 초기 로딩 시간을 단축하고자 한다.&#x20;



### SplitChunksPlugin

[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/#root) 은 코드 중복을 최소화하고 애플리케이션 성능을 최적화하기 위해 공통 모듈을 자동으로 추출하여 별도의 청크 파일로 분리하는 플러그인이다. 각 설정에 대한 자세한 설명은 [여기](https://webpack.kr/plugins/split-chunks-plugin/#optimizationsplitchunks)를 참고하길 바란다. 우리는 웹팩에 아래와 같은 설정을 추가하여, 기존과 번들파일이 어떤식으로 달라지는지 확인해보았다.  기본값에 대하여 설정을 생략 하고, 아래와 같이 코드를 작성하더라도, node\_modules 의 모듈들이 chunk 로 분리된걸 확인하였지만, 각 설정들에 대하여 모든 팀원들이 바로 알 수 있도록 요약된 설명과 공식문서 링크를 주석으로 달았다.

```javascript
splitChunks: {
      chunks: "all",
    },
```



모든 팀원들이 알 수 있도록 요약된 설명과 공식문서 링크를 주석으로 건 코드

```javascript
optimization: {
    minimizer: [
      // `...`는 기존의 minimizer 옵션을 확장하기 위해서 사용, 생략하면 기본값인 terser plugin 등이 생략되기 때문에 JS의 minify가 되지 않습니다.
      // CssMinimizerPlugin (https://webpack.js.org/plugins/css-minimizer-webpack-plugin/)
      `...`,
      new CssMinimizerPlugin(),
    ],
    // splitChunks는 코드를 분할하는 방법을 설정합니다. (https://webpack.js.org/plugins/split-chunks-plugin/)
    splitChunks: {
      chunks: "all", // all, async, initial 중 하나를 설정합니다.
      // all : 모든 종류의 청크에서 공유된 모듈을 분리합니다.
      // async : 비동기로 로드되는 모듈에서 공유된 모듈을 분리합니다.
      // initial : 초기 로딩 시 로드되는 모듈에서 공유된 모듈을 분리합니다.
      minSize: 20000, // 최소 청크 크기 (기본값: 20000 bytes)
      minRemainingSize: 0, // 남아 있는 청크 최소 크기 (기본값: 0)
      minChunks: 1, // 최소 공유 청크 수 (기본값: 1)
      maxAsyncRequests: 30, // 최대 비동기 청크 요청 수 (기본값: 30)
      maxInitialRequests: 30, // 최대 초기 청크 요청 수 (기본값: 30)
      enforceSizeThreshold: 50000, // 강제 사이즈 임계값 (기본값: 50000 bytes)
      cacheGroups: {
        // 청크 그룹 설정 (기본값)
        defaultVendors: {
          test: /[\\/]node_modules[\\/]/, // node_modules 폴더에 있는 모듈을 대상으로 합니다.
          priority: -10, // 우선순위를 설정합니다.
          reuseExistingChunk: true, // 이미 존재하는 청크를 재사용합니다.
        },
        default: {
          minChunks: 2, // 최소 공유 청크 수를 설정합니다.
          priority: -20, // 우선순위를 설정합니다.
          reuseExistingChunk: true, // 이미 존재하는 청크를 재사용합니다.
        },
      },
    },
  },
```



<figure><img src="../.gitbook/assets/image (31).png" alt=""><figcaption><p>bundle.main.js 크기가 stat 기준 2.25 MB 로 줄어든 모습</p></figcaption></figure>





우리는 웹팩 설정들에 대하여 각 옵션들에 대해 모두 공식 문서 링크와 간단한 설명을 주석으로 적어놓았다. 왜냐하면 다른 개발자들이 보더라도 웹팩 관련된 설정들에 대하여 쉽게 이해하고 파악할 수 있게 하기 위함이었다.&#x20;



하지만 이것만으로 부족하다.  왜냐하면 네트워크 탭을 보다시피 초기 페이지 진입시에, 유저가 사용하고 있지 않은 번들 파일이 로드 되기 때문이다. 이 defaultVendors 는 하나의 캐시그룹으로 새로 생성되는 대신 재사용된다.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>



