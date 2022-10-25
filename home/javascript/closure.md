---
description: 클로저란 무엇인가
---

# Closure

### 클로저란?

함수와 함수가 선언된 어휘적 환경의 조합이다.\
클로저란 자신이 생성될 때의 '환경'을 기억하는 '함수'다.\
클로저란 자신이 생성될 때의 스코프에서 알 수 있었던 변수를 기억하는 '함수'이다.\
정의만 봐서는 정확히 이해하기 힘들다.\
우리는 클로저를 이해하기 위해\
JavaScript 가 어떻게 변수의 유효 범위를 지정하는지(Lexical scoping) 를 먼저 이해해야 한다.



### 렉시컬 스코핑(Lexical Scoping) 이란?

함수를 '호출' 할 때가 아니라, 함수를 어디에 '선언' 하는지에 따라 결정된다. \
이를 렉시컬 스코핑이라 한다.

```javascript
  const num = 5;

  const outerFunc = () => {
    const num = 10;
    const innerFunc = () => {
      console.log("num는", num); // 5 일까요? 10일까요?
    };
    return innerFunc;
  };

  const inner = outerFunc();

  inner();
```
