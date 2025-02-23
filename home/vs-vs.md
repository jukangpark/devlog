---
description: 동기 vs 비동기, 그리고 블로킹 vs 논블로킹의 차이점은 주로 코드 실행 흐름과 자원의 처리 방식에서 나타난다.
cover: >-
  https://images.unsplash.com/photo-1559249875-05d44554edd2?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw0fHxibG9ja3xlbnwwfHx8fDE3MjExMDQyNTl8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 동기 vs 비동기, 블로킹 vs 논블로킹

## 동기(Synchronous) vs 비동기(Asynchronous)

### 1. 동기(Synchronous)

• 동기 방식은 작업이 순차적으로 처리된다. 하나의 작업이 완료되기 전까지는 다음 작업이 시작되지 않는다.

• 예를 들어, A 작업이 끝날 때까지 B 작업은 대기하며, A가 완료된 후에야 B가 실행된다. 개발자 관점에서 코드가 위에서 아래로 순차적으로 실행되는 형태다.

• 장점: 코드가 직관적이고 예측 가능하다.

• 단점: 하나의 작업이 오래 걸리면 전체 프로그램이 느려질 수 있다.



### 2. 비동기(Asynchronous)

• 비동기 방식은 작업이 완료될 때까지 기다리지 않고 다음 작업을 바로 시작한다.

• 예를 들어, A 작업이 시작되고, A가 끝나기를 기다리지 않으며 B 작업을 동시에 처리할 수 있다. A가 끝나면 그 결과가 나중에 처리된다.

• 장점: 응답 시간이 길거나 외부 자원(파일 I/O, 네트워크 요청 등)을 사용할 때 효율적이다.

• 단점: 코드 흐름이 복잡할 수 있으며, 콜백 지옥이나 복잡한 비동기 패턴을 관리해야 한다.

\




## 블로킹(Blocking) vs 논블로킹(Non-Blocking)

### 1. 블로킹(Blocking)

• 블로킹 방식은 요청을 보내고 그 응답이 올 때까지 그 스레드가 아무것도 하지 않고 기다린다.

• 예를 들어, 파일을 읽는 작업이 끝날 때까지 다른 작업을 진행할 수 없고, 그 스레드는 멈춰 있다.

• 장점: 코드가 간단하고 직관적이다.

• 단점: I/O 작업 등에서 리소스가 비효율적으로 사용될 수 있다.



### 2. 논블로킹(Non-Blocking)

• 논블로킹 방식은 요청을 보내고 즉시 반환되며, 작업이 완료되었을 때 별도의 메커니즘(콜백, 이벤트 등)을 통해 결과를 처리한다.

• 예를 들어, 파일을 읽는 요청을 보내고, 그 작업이 완료될 때까지 스레드는 다른 작업을 처리할 수 있다. 파일 읽기가 끝나면 해당 결과만 별도로 처리한다.

• 장점: 리소스를 효율적으로 사용할 수 있고, 병렬 작업이 가능하다.

• 단점: 논블로킹 코드는 동시성 처리가 필요한 상황에서 복잡해질 수 있다.

\


### 정리

• 동기 vs 비동기는 작업을 처리하는 시간적인 흐름에 대한 개념이다.

• 블로킹 vs 논블로킹은 자원을 사용하는 방식에 대한 개념이다.

\


동기/비동기와 블로킹/논블로킹은 서로 독립적이지만, 조합하여 사용될 수 있다. \
예를 들어, 동기/논블로킹, 비동기/블로킹 방식도 가능하다. 이 둘은 독립적인 개념으로, 다음과 같은 조합이 가능하다:



• 동기 블로킹: 작업이 끝날 때까지 기다리고, 스레드는 다른 작업을 하지 않는다.

• 비동기 블로킹: 작업이 완료되기를 기다리지 않지만, 스레드가 자원 사용을 끝낼 때까지 기다린다.

• 동기 논블로킹: 작업이 끝날 때까지 기다리지만, 스레드는 다른 자원을 사용하며 기다린다.

• 비동기 논블로킹: 작업을 기다리지 않으며, 스레드도 자원에 묶이지 않는다.
