---
description: 효율적인 E2E 테스트는 어떻게 작성할 수 있을까
cover: >-
  https://images.unsplash.com/photo-1454165804606-c3d57bc86b40?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw5fHx0ZXN0fGVufDB8fHx8MTcyMDU5NTEzN3ww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 테스트를 더 효율적으로 할 수 없을까?

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption><p>Builder R3 v1.0 기능 리스트</p></figcaption></figure>



데이터 시각화 편집툴인 Builder R3 의 출시를 앞두고, \
기능에 대한 검증을 하기 위해 v1 기능리스트를 기반으로 내가 작성한 테스트 시나리오를 기준으로 \
우리팀에서 먼저 테스트를 진행하였다



<figure><img src="../.gitbook/assets/image (16).png" alt=""><figcaption><p>테스트 시나리오를 기반으로 테스트를 진행</p></figcaption></figure>



테스트를 진행하다 보니, 여러가지 문제에 부딪혔다.\
사람마다 테스트의 결과가 달랐고, 테스트 결과에 자신의 주관이 들어갔다



회사에 전문 기획자가 없었고, 각 기능에 대하여 개발자들이 정의하고, 기획한 내용들이 많아서\
이런 내용들이 모두 공유되지 않았기 때문에 이런 문제점들이 발생하였다.



그리고 각 테스트를 진행하는 사람들의 환경이 너무 달랐고, (OS, 브라우저 등등)\
업무 생산성이 떨어진다는 느낌을 받았다.



회사에서 E2E 자동화 테스트 툴을 사용하여 테스팅을 했던 사례가 없어서, \
PlayWright 나 Cypress 같은 툴을 활용하여 자동화 테스트를 사용해보고 \
사내 테스트 효율성을 더 극대화 하고 싶었다.



[https://playwright.dev/](https://playwright.dev/)\
[https://www.cypress.io/](https://www.cypress.io/)



