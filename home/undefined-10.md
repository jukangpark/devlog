---
description: 브라우저가 어떤 과정을 거쳐 화면을 그리는지 알아보자!
cover: >-
  https://images.unsplash.com/photo-1642252429939-3f9232959eb9?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw3fHxwYWludHxlbnwwfHx8fDE3MjE1NDE5MDV8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 브라우저는 화면을 어떻게 그리는가

### 브라우저 렌더링 과정

`DOM + CSSOM > 렌더 트리 > 레이아웃 > 페인트 > 컴포지트`

브라우저는 기본적으로 위와 같은 과정을 거쳐 화면을 그린다. 이러한 과정을 Critical Rendering Path 또는 Pixel Pipeline 이라고 한다. 이번 글에서는 각 과정에 대해 알아보고자 한다.





### DOM + CSSOM

가장 처음에는 HTML 과 CSS 등 화면을 그리는 데 필요한 리소스를 다운로드 한다. 다운로드한 HTML 은 브라우저가 이해할 수 있는 형태로 변환하는 parsing 과정을 거친다. 그렇게 해서 요소 간의 관계가 tree 구조로 표현되어 있는 [**DOM(Document Object Model)**](https://developer.mozilla.org/ko/docs/Glossary/DOM)을 만든다.

```
// 이미지 첨부하기
```

마찬가지로 CSS 도 브라우저가 이해할 수 있는 형태로 변환된다. 변환 결과, [**CSSOM(CSS Object Model)**](https://developer.mozilla.org/ko/docs/Glossary/CSSOM)이라는 트리 구조가 생성된다. CSSOM은 각 요소가 어떤 스타일을 포함하고 있는지를 나타낸다.

```
// 이미지 첨부하기
```





### 렌더트리 (render tree)

렌더 트리는 DOM 과 CSSOM 의 결합으로 생성된다. 이 렌더 트리는 화면에 표시되는 각 요소의 레이아웃을 계산하는 데 사용된다. 달리 말하면,&#x20;





### 레이아웃 (layout)





### 페인트 (paint)





### 컴포지트 (composite)





### 직접 확인해보자!





### 리플로우 vs 리페인트





### 하드웨어 가속 (GPU 가속)



