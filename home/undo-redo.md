---
description: 아니 피그마에서 구현해놓은걸 우리가 할 수 있을까요? 어떻게 접근해야될까요.. 3주동안의 여정
cover: >-
  https://images.unsplash.com/photo-1533749047139-189de3cf06d3?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHwxfHxjbG9ja3xlbnwwfHx8fDE3MjA1OTQwNzh8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# 서로의 행위를 존중해주는 실시간 undo / redo (작성중)

### 뒤로가기 / 앞으로가기 기능의 제작 계기

기존의 Builder R2 에는 뒤로가기 / 앞으로가기 기능이 존재하지 않았기 때문에 우리는 Builder R3 에서 뒤로 가기 / 앞으로 가기를 구현하고 싶었는데, 문제는 동시성이었다. 우리는 **동시 협업툴**을 제작하고 있었기 때문에 피그마와 동일한 UX 로 뒤로 가기 / 앞으로가기를 구현해내야만 했다. **어떻게 해야 다른 유저의 행위를 존중해주면서 나의 행위를 뒤로 돌리고 앞으로 가지?** 우리는 3주동안 코드 작성을 한줄도 못한채 어떤 구조와 방향성을 가져가야할지 고민했다. 그 여정(삽질)에 대해 다루고자 한다. [Builder R3](https://jkng-96.gitbook.io/devlog/home/undefined) 에 대해서는 여기를 참조하길 바란다. 설명의 편의성을 위해 뒤로 가기 앞으로가기를 이제 Undo, Redo 로 부르겠다.



### Undo and Redo 란?

1. Undo : 사용자의 작업을 무효화하고 이전 상태로 되돌리는 것
2. Redo : 이미 되돌린 작업을 다시 수행하는 것



<figure><img src="../.gitbook/assets/image (36).png" alt=""><figcaption><p>Undo and Redo</p></figcaption></figure>



### Undo & Redo 를 구현하는 방법에 대한 고민

Undo & Redo 를 구현하는 방법은 다양하다. 우선 State 를 저장하는 방법이다. 전체 State 를 저장하여 해당 상태로 되돌리는 방법이 존재한다. 만약 우리가 동시성을 고려하지 않았다면 이 방향성대로 쉽게 구현하였을 것이다.

<figure><img src="../.gitbook/assets/image (37).png" alt=""><figcaption><p>전체 State 를 저장하고 해당 상태로 되돌리는 방법</p></figcaption></figure>



또 다른 방법으로는 `Delta-State 저장`이 있다. State 의 변경 사항만 저장하는 것이다.

<figure><img src="../.gitbook/assets/image (38).png" alt=""><figcaption><p>Delta-State 저장, State 의 변경 사항만 저장한다.</p></figcaption></figure>



Action 을 저장하는 방법도 있다. 역역산 (invert operation) 을 통해 구현하는 방법이다.

<figure><img src="../.gitbook/assets/image (39).png" alt=""><figcaption><p>역역산을 통해 구현하는 방법</p></figcaption></figure>



마지막으로 하이브리드 방법이다. 특정 시점의 스냅샷 존재, 외에는 action 을 저장한다. 가장 가까운 시점의 snapshot 을 찾고 이후의 action 을 재수행한다.

<figure><img src="../.gitbook/assets/image (40).png" alt=""><figcaption><p>하이브리드 방법</p></figcaption></figure>



\[TODO] 어떤 방식을 선택했는지 작성하기



### Undo & Redo 구현시 사용하는 자료구조

어떤 자료구조를 선택하는 것이, 우리의 프로젝트에서 가장 효율적으로 구현해낼 수 있을지 고민하였다.

<figure><img src="../.gitbook/assets/image (41).png" alt=""><figcaption><p>undo redo 구현시 사용할 수 있는 다양한 자료구조</p></figcaption></figure>

각 자료구조의 특징은 아래와 같다.

1. Undo stack, Redo stack
2. History stack with pointer
3. Doubly linked list



### 활용한 디자인 패턴









### 실시간 협업 툴의 뒤로가기 / 앞으로가기 분석



### 어떻게 다른 사용자의 행위를 존중해주면서 뒤로 가기 / 앞으로 가기 할 수 있을지에 대한 고민



### 코드로 구현 : 싱글턴 패턴과 스택 사용하기



