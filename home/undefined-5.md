---
description: 웹 성능 지표 개선 해보자!
cover: >-
  https://images.unsplash.com/photo-1517026575980-3e1e2dedeab4?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw1fHxHYXVnZXxlbnwwfHx8fDE3MjA1OTMzMTd8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 웹 성능 지표 개선하기 (작성중)

### 대시보드 빌더 R3 프로젝트 성능 최적화 개요

우리는 대시보드 빌더 R3 프로젝트의 프론트엔드 성능을 개선하기 위해 \
[Google Developers Lighthouse](https://developer.chrome.com/docs/lighthouse) 를 사용하여 분석을 진행하였다.\
Lighthouse 의 다양한 성능 지표를 통해 현재 프로젝트의 상태를 평가하고, \
최적화 작업의 결과를 기록하였다.



Lighthouse 를 사용하여 웹 페이지를 측정하게 되면 \
종합 성능 점수가 매겨지는데, 이 점수는 5개의 **지표 (metris)**에 가중치를 적용해 평균 낸 점수이다.\
이러한 지표를 **웹 바이탈 (Web Vitals)** 이라고 부른다.

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>5개의 지표들(Metrics)과 가중치(Weighting)</p></figcaption></figure>

### 빌더 성능 지표 검사 2024 년 7월 8일

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption><p>2024 년 7월 8일 Light House 측정 결과</p></figcaption></figure>

{% file src="../.gitbook/assets/빌더 성능지표 검사 24.07.08 (1).pdf" %}

### First Contentful Paint (FCP)

[FCP](https://developer.chrome.com/docs/lighthouse/performance/first-contentful-paint) 는 페이지가 로드될 때 브라우저가 DOM 컨텐츠의 첫 번째 부분을 렌더링 하는 데 걸리는 시간에 관한 지표이다.

* 페이지에 진입하여 첫 콘텐츠가 뜨기 까지 0.6초가 걸렸음을 알 수 있다. \


### Largest Contentful Paint (LCP)

[LCP](https://developer.chrome.com/docs/lighthouse/performance/lighthouse-largest-contentful-paint) 는 페이지가 로드될 때 화면 내에 있는 가장 큰 이미지나 텍스트 요소가 렌더링되기까지 걸리는 시간을 나타내는 지표이다. 위 결과에서 LCP 는 6.6 초가 걸렸음을 알 수 있다.\
그런데 아래의 이미지를 같이 참조하여 보면 알겠지만, h1 태그를 기준으로 6.6초가 걸림을 알 수 있다.\
h1 요소가 LCP 지표의 기준으로 사용되는 이유는 h1 태그가 중요한 콘텐츠라고 생각해서 그런 거 같다.\
그렇기 때문에 시맨틱한 HTML 태그를 준수하는 코드를 짜야함을 다시 한번 깨닫게 되었다.\


<figure><img src="../.gitbook/assets/image (6) (1).png" alt=""><figcaption><p>LCP 의 기준이 왜 h1 요소일까</p></figcaption></figure>

### Total Blocking Time (TBT)

[TBT](https://developer.chrome.com/docs/lighthouse/performance/lighthouse-largest-contentful-paint) 는 페이지가 클릭, 키보드 입력 등의 사용자 입력에 응답하지 않도록 차단된 시간을 총합한 지표이다.\
측정은 FCP 와 TTI 사이의 시간 동안 일어나며 메인 스레드를 독점하여 다른 동작을 방해하는 작업에 걸린 시간을 총합한다.\


* 700ms 라는 결과값이 나왔고, 어떤 이유로 Bloking TIme 이 700ms 나 되는지 원인을 찾아봐야겠다.



### Cumulative Layout Shift (CLS)

[CLS](https://web.dev/articles/cls) 는 페이지 로드 과정에서 발생하는 예기치 못한 레이아웃 이동을 측정한 지표이다. 레이아웃 이동이란 화면상에서 요소의 위치나 크기가 순간적으로 변하는 것을 말한다.



* 예기치 못한 레이아웃 이동은 없는 거 같다.



### Speed Index (SI)

[SI](https://developer.chrome.com/docs/lighthouse/performance/speed-index) 는 페이지 로드 중에 콘텐츠가 시각적으로 표시되는 속도를 나타내는 지표이다.\


* 어째서 4.6초나 걸렸는지 찾아보고 개선해야 할 거 같다.\


### View Treemap

Lighthouse 에서 View Treemap 을 클릭하게 되면 아래의 이미지 처럼,\
마치 [webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer) 와 비슷한 트리맵 이미지를 얻을 수 있다. \
현재 프로젝트의 모듈 번들링과 사용하고 있는 모듈, 사용하고 있지 않은 모듈을 한눈에 볼 수 있어 좋다.



<figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption><p>All : 33.4 MiB</p></figcaption></figure>

<figure><img src="../.gitbook/assets/image (3) (1) (1) (1) (1) (1).png" alt=""><figcaption><p>Unused Bytes: 17.8 MiB</p></figcaption></figure>



### 대시보드 빌더 R3 프로젝트 성능 최적화 계획

우리는 Lighthouse 분석 결과를 바탕으로 각 성능 지표에 대한 개선 작업을 진행할 것이다.\
각 지표에 대한 개선 작업 계획은 다음과 같았다.



1. FCP\
   개선 계획 : \
   불필요한 JavaScript 비동기 로드 및 지연 로드\
   이미지 최적화 및 적절한 포맷 사용하기
2. LCP\
   개선계획 : \
   올바른 Largest Content 의 기준 정하기 ( 시맨틱 HTML )\
   클라이언트 측 렌더링 성능 개선
3. TBT\
   개선계획 : \
   긴 JavaScript 작업을 작은 단위로 나누어 처리\
   효율적인 코드 분할 및 로딩 전략 적용\
   사용하지 않는 폴리필 및 라이브러리 제거\
   \




