---
description: 병목현상 찾아서 개선하기
cover: >-
  https://images.unsplash.com/photo-1535579386897-af3bfc73d110?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxMHx8U3BlZWR8ZW58MHx8fHwxNzIxMzE0Nzg1fDA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Runtime Performance 를 분석해보자

### Performance 탭을 잘 분석할 수 있어야 하는 이유

[Performance](https://developer.chrome.com/docs/devtools/performance) 탭을 통해 페이지의 렌더링이 얼마나 효율적인지, 레이아웃과 페인트가 얼마나 자주 발생하는지, 자바스크립트 성능을 분석하고 병목현상을 찾아 이를 최적화 할 수 있다. 그리고 무엇보다 효율적인 디버깅이 가능하며, 다음과 같은 문제를 식별하고 해결할 수 있다.&#x20;

1. 메모리 누수 : 메모리 사용량을 모니터링하여 메모리 누수를 발견하고, 이를 수정할 수 있다.
2. 장시간 실행 작업 : 자바스크립트 함수나 작업이 너무 오래 실행되어 UI 가 중단되는 문제를 찾고 해결할 수 있다.
3. 비효율적인 코드 : 자주 호출되는 함수나 비효율적으로 작성된 코드를 찾아 최적화 할 수 있다.

웹 페이지의 성능을 최적화 하는 것은 사용자 경험과 직결되는 문제이고, 개발자는 최고의 사용자 경험을 제공할 수 있어야 한다. 회사내의 프로젝트들 중 병목현상을 발견하여 이를 분석하고 원인을 파악하여 해결하고, 기여한 내용에 대해 작성해보고자 한다. 119 구조대 출동\~





### 병목현상이 있는거 같아요

우리의 신제품에 병목현상이 있다는 걸 들었다. 원인을 파악하기 위해 나는 문제가 발생한 페이지에서 Runtime Performance 를 측정하였다.





### 크롬 개발자 도구 Performance 패널 정복하기

각 지표들이 어떤걸 의미하는지에 대해 알아보자! 눈이 좋아야 몸이 고생하지 않는다.







#### CPU 차트, Network 차트, 스크린샷



#### Network 타임라인



#### Frames, Timings, Main



#### 하단 탭





### 페이지 로드 과정 살펴보기





### 현상 파악하기



### 원인 파악하기



###

