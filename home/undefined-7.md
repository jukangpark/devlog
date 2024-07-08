---
description: 웹 성능 지표 개선을 왜 해야하지? 유저는 예민해 ㅋ
---

# 페이지가 너무 늦게 뜨잖아요?

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>Performance Insights</p></figcaption></figure>

Lighthouse 패널에서 웹 성능 지표 측정하기



### First Contentful Paint (FCP)

사용자에게 처음으로 콘텐츠가 표시되는 시점을 의미\
콘텐츠는 텍스트, 이미지, 비디오 등 페이지의 주요 요소를 포함



### Largest Contentful Paint (LCP)

가장 큰 콘텐츠 요소가 화면에 렌더링되는 시점을 의미함. \
이 요소는 보통 이미지, 비디오 또는 큰 텍스트 블록임



### First Input Delay (FID)

사용자가 페이지에 처음으로 인터랙션(클릭, 탭 등)을 시도할 때까지의 시간



### Time to Interactive (TTI)

페이지가 완전히 상호작용 가능한 상태가 되는 데 걸리는 시간\
이 시점 이후에는 사용자가 페이지와 자유롭게 상호작용할 수 있음



### Total Blocking Time (TBT)

FCP와 TTI 사이에 메인 스레드가 차단된 시간의 합



### Cumulative Layout Shift (CLS)

페이지 요소의 예상치 못한 레이아웃 이동을 측정함 \
시각적 안정성을 나타냄

***



<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>2024 년 7월 8일 Light House 측정 결과</p></figcaption></figure>



### 코드 스플리팅과 lazy loading



[https://webpack.js.org/guides/code-splitting/#root](https://webpack.js.org/guides/code-splitting/#root)

[https://webpack.js.org/guides/lazy-loading/#root](https://webpack.js.org/guides/lazy-loading/#root)







