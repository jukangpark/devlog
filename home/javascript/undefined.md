---
description: 기존과 달라진 ES6 에서의 순회와 이터러블 / 이터레이터 프로토콜
---

# 이터러블 / 이터레이터 프로토콜

<figure><img src="../../.gitbook/assets/스크린샷 2022-10-25 오후 5.26.10.png" alt=""><figcaption><p>이터러블 / 이터레이터 프로토콜</p></figcaption></figure>

<figure><img src="../../.gitbook/assets/스크린샷 2022-10-25 오후 5.29.34.png" alt=""><figcaption></figcaption></figure>



### 이터러블 (iterable)

> 이터레이터를 리턴하는 \[Symbol.iterator]\() 메서드를 가진 객체

이터러블 프로토콜을 준수한 객체를 이터러블이라고 부른다.\
이터러블은 Symbol.iterator 메서드를 구현하거나, \
또는 프로토타입 체인에 의해 상속한 객체를 말한다. \
예를 들어 배열은 Symbol.iterator 메서드를 Array.prototype 에 의해 상속받아\
소유하고 있기 때문에, 이터러블 프로토콜을 준수하는 이터러블이다.\


### 이터레이션 프로토콜 (iteration protocol)

데이터 컬렉션을 순회하기 위한 프로토콜(미리 약속된 규칙) 이다.\
이터레이션 프로토콜을 준수하는 객체는 for ..of 문으로 순회할 수 있고, \
Spread 문법의 피연산자가 될 수 있다.





2022/10/25 추후 업데이트 할 예정







