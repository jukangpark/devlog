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

[SplitChunksPlugin](https://webpack.js.org/plugins/split-chunks-plugin/#root) 은 코드 중복을 최소화하고 애플리케이션 성능을 최적화하기 위해 공통 모듈을 자동으로 추출하여 별도의 청크 파일로 분리하는 플러그인이다. 각 설정에 대한 자세한 설명은 [여기](https://webpack.kr/plugins/split-chunks-plugin/#optimizationsplitchunks)를 참고하길 바란다. 우리는 웹팩에 아래와 같은 설정을 추가하여, 기존과 번들파일이 어떤식으로 달라지는지 확인해보았다.  기본값에 대하여 설정을 생략 하고, 아래와 같이 기본값을 생략한 코드를 작성하더라도, node\_modules 의 모듈들이 chunk 로 분리된걸 확인했지만, 이해를 돕기 위해 각 설정들에 대하여 요약된 설명과 공식문서 링크를 주석으로 달았다.

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

node\_modules 폴더에 있는 모듈을 대상으로 chunk 를 하였기 때문에, ,bundle.397.js 에는 chunk 된 모듈이 하나의 번들로 존재하는 걸 확인할 수 있었다. 하지만 이것만으로 부족하다.  왜냐하면 네트워크 탭을 보다시피 초기 페이지 진입시에, 유저가 사용하고 있지 않은 번들 파일이 로드 되기 때문이다. 이 defaultVendors 는 하나의 캐시그룹으로 새로 생성되는 대신 재사용된다.

<figure><img src="../.gitbook/assets/image (32).png" alt=""><figcaption></figcaption></figure>

하지만 여전히 최초에 불러오는 번들 사이즈가 너무 크다. 왜냐하면 defaultVendors 는 node\_modules 를 청크로 분리한 번들 파일인데, 이 번들 파일은 현재 유저가 사용하지 않아도 되는 청크 파일들을 모두 포함하고 있기 때문이다. 따라서 우리는 Dynamic Imports 를 사용하여 청크된 번들파일을 필요할 때에 최초로 한번 가져오는 방향으로 최적화 해주었다.



### **Dynamic Imports**

[Dynamic Imports](https://webpack.kr/guides/code-splitting/#dynamic-imports) 는 [import()](https://webpack.kr/api/module-methods/#import-1) 구문을 사용하는 방식을 사용해서 코드 스플리팅을 하였다. import 구문을 가지고 모듈을 동적으로 로드한다. import() 에 대한 호출은 분할 지점으로 처리되어, 요청된 모듈과 그 자식은 별도의 청크로 웹팩이 알아서 분할해준다. 이 구문은 내부적으로 Promise 에 의존한다. import() 는 최소한 모듈 위치에 대한 정보를 포함해야 한다. 그리고 런타임에 만약 import 문으로 가져와서 변수에 담아놓은 해당 변수가 계산되면 해당 파일이 사용가능하도록 번들 파일을 가져온다



우선 R3 프로젝트에서 최초 로드시 필요한 모듈을 제외하기 시작하였다. 프로젝트 단위가 커서, 한꺼번에 전부 제외하기는 힘들었고, 우선 순위를 두고 작업을 하기 시작했다. getLayerBySubType.jsx 에서 아래와 같이 subType 에 따라 렌더링 하게 되는 각각의 레이어들을 청크로 나누기 위해 import 구문을 사용하고, 해당 모듈의 경로를 작성해주었다. 이렇게만 작업을 하게 되면 각 모듈들을 동적으로 불러오게 되기 때문에 getLayerBySubType 를 호출하는 곳에서 에러가 발생하였다.&#x20;

```javascript
// Lazy load components
const ImageLayer = React.lazy(() => import("@nodes/layers/ImageLayer"));
const DataImageLayer = React.lazy(() => import("@nodes/layers/DataImageLayer"));
const VideoLayer = React.lazy(() => import("@nodes/layers/VideoLayer"));
const EChartLayer = React.lazy(() => import("@nodes/layers/chart/EChartLayer"));
const AgGridLayer = React.lazy(() =>
  import("@nodes/layers/agGrid/AgGridLayer")
);
const RectLayer = React.lazy(() => import("@nodes/layers/RectLayer"));
const TextLayer = React.lazy(() => import("@nodes/layers/TextLayer"));
const DataTextLayer = React.lazy(() => import("@nodes/layers/DataTextLayer"));
const DoldoriLayer = React.lazy(() =>
  import("@nodes/layers/doldori/DoldoriLayer")
);
const LinkTabLayer = React.lazy(() => import("@nodes/layers/LinkTabLayer"));
const LinkTabToggleSwitchLayer = React.lazy(() =>
  import("@nodes/layers/LinkTabToggleSwitchLayer")
);
const LegendLayer = React.lazy(() => import("@nodes/layers/LegendLayer"));
const TimeLineLayer = React.lazy(() => import("@nodes/layers/TimeLineLayer"));
const CustomReactComponentLayer = React.lazy(() =>
  import("@nodes/layers/custom/CustomReactComponentLayer")
);
const TimeLayer = React.lazy(() => import("@nodes/layers/TimeLayer"));
const HexagonChartLayer = React.lazy(() =>
  import("@nodes/layers/chart/HexagonChartLayer")
);
```



<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

Uncaught Error: A component suspended while responding to synchronous input. This will cause the UI to be replaced with a loading indicator. To fix, updates that suspend should be wrapped with startTransition.\
\
이 오류 메시지는 React 18에서 도입된 새로운 동시성 기능과 관련이 있다. 특히, 동적 import() 문을 사용하여 비동기적으로 컴포넌트를 로드할 때 발생할 수 있는 문제이다. React는 비동기적으로 로드되는 컴포넌트가 로드되는 동안 UI를 어떻게 처리할지 제어하는 메커니즘을 제공한다. 이 에러 메시지는 특정 컴포넌트가 동적 import()를 사용하여 비동기적으로 로드되는 동안 서스펜딩되었고, 이로 인해 UI가 로딩 인디케이터로 대체되었음을 의미한다. React는 이러한 상황을 피하기 위해, 이러한 업데이트를 startTransition으로 래핑할 것을 권장한다. 이 문제를 해결하기 위해, startTransition을 사용하여 비동기 업데이트를 래핑해야 한다. startTransition은 비동기 작업을 낮은 우선순위로 처리하여, 사용자가 동기 작업을 계속 수행할 수 있도록 한다.





### Suspense

[Suspense](https://react.dev/reference/react/Suspense#suspense) 는 리액트 16.6 버전에서부터 실험 버전으로 도입된 기능으로, 컴포넌트를 동적으로 가져올 수 있게 도와주는 기능이다. 이 Suspense 는 React.lazy 를 통해 지연시켜 불러온 컴포넌트를 렌더링 하는 역할을 하게 된다. Suspesne 는 크게 2개의 인자를 받는데, 하나는 fallback props 로, 지연시켜 불러온 컴포넌트를 미처 불러오지 못했을 때 보여주는 fallback 을 나타낸다. 그리고 children 으로 React.lazy 로 선언한 지연 컴포넌트를 받는다. 정리하면, 지연 컴포넌트를 로딩하기 전에는 fallback 을 보여주고, 이 lazy 로 불러온 컴포넌트가 지연 로딩이 완료되면 fallback 대신 비로소 해당 컴포넌트를 보여주게 된다.





### React.lazy

[`lazy`](https://react.dev/reference/react/lazy) lets you defer loading component’s code until it is rendered for the first time.

lazy 를 사용하면 컴포넌드가 처음 렌더링 될 때까지 로딩을 연기할 수 있다.

* `load`: A function that returns a [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global\_Objects/Promise) or another _thenable_ (a Promise-like object with a `then` method). React will not call `load` until the first time you attempt to render the returned component. After React first calls `load`, it will wait for it to resolve, and then render the resolved value’s `.default` as a React component. Both the returned Promise and the Promise’s resolved value will be cached, so React will not call `load` more than once. If the Promise rejects, React will `throw` the rejection reason for the nearest Error Boundary to handle.

lazy 에 파라미터는 load 라고 하는 Promise 를 반환하는 함수이다. React 는 반환된 컴포넌트를 처음 렌더링 할 때까지 load 함수를 호출하지 않는다. React 가 먼저 load 함수를 호출한 후 resolve 가 될 때까지 기다렸다가 해결된 값의 .default 를 Reat 컴포넌트로 렌더링한다. 반환된 Promise 의 resolved 된 값이 모두 캐시되므로 React 는 load 함수를 두 번 이상 호출하지 않는다. Promise 가 만약 reject 되면 React 는 가장 가까운 Error Boundary 에서 rejections reason 을 throw 할것이다.



우리는 아래와 같이 getLayerBySubType 을 호출하는 곳에서, Suspense 로 감싸고 fallback 은 null 을 넣어주었다. 왜냐하면 우리의 프로젝트 특성상 fallbackUI 를 보여줄 필요가 없었기 때문이었다.

```javascript
          <Suspense fallback={null}>
            {getLayerBySubType(subType, {
              id,
              width: roundedWidth,
              height: roundedHeight,
              backgroundColor,
              property,
              node,
              isText,
              globalVariables,
              pageType,
              opacity,
              responseData,
              setProject,
              isWS,
              isHTTP,
            })}
          </Suspense>
```

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>잘 분리된 chunk 파일들</p></figcaption></figure>

그러나 util 폴더에서 사용하고 있는 공통 모듈들이 각 chunk 파일에서 중복으로 존재하는걸 발견하였다. 그래서 우리는 2번 이상 재 사용되는 모듈에 대하여 utils 폴더에 있는 모듈들을 splitChunkPlugin 의 범위에 포함시키는 코드를 작성하고 다시 build 하여서 아래와 같이 공통적으로 사용되는 모듈을 청크로 분리하여서 중복을 제거하였다.



```javascript
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
        utilModules: {
          // `utilModules`는 임의로 지정한 그룹 이름입니다.
          test: /[\\/]util[\\/]/, // `util` 폴더에 있는 모든 모듈을 타겟팅합니다.
          name: "utils", // 생성될 청크의 이름을 지정합니다.
          priority: -5, // 기본 그룹보다 약간 높은 우선순위를 부여합니다.
          reuseExistingChunk: true, // 이미 존재하는 청크를 재사용합니다.
        },
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
```



<figure><img src="../.gitbook/assets/image (33).png" alt=""><figcaption><p>SplitChunksPlugin 으로 2번 이상 호출되는 모듈들에 대하여 utils 라고 하는 청크 이름으로 분리됨</p></figcaption></figure>



<figure><img src="../.gitbook/assets/image (34).png" alt=""><figcaption><p>깔끔하게 util 내부의 중복으로 포함되던 모듈들이 chunk 번들 파일에서 제거된 모습</p></figcaption></figure>





