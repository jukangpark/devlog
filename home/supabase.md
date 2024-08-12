---
description: 파이어 베이스를 대체하겠다고?
cover: >-
  https://images.unsplash.com/photo-1590859808308-3d2d9c515b1a?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw3fHxkYXRhYmFzZXxlbnwwfHx8fDE3MjMyNjIyOTJ8MA&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Supabase 를 활용한 릴리즈 주도 개발 (작성중)

<figure><img src="../.gitbook/assets/image (44).png" alt=""><figcaption><p>수파베이스!</p></figcaption></figure>

### Supabase 란 무엇인가

[supabase](https://supabase.com)  슬로건은 Build in a weeked Scale to millioins 이다. 개발 생태계는 역시 슬로건을 잘 지어야한다. 후킹을 하고 싶었던 거면 우선 성공이다.&#x20;

> Supabase is an open source Firebase alternative. Start your project with a Postgres database, Authentication, instant APIs, Edge Functions, Realtime subscriptions, Storage, and Vector embeddings.

Supabase 는 Postgres Database 와 Auth 그리고 EdgeFunction, RealTime, Storage 등등 정말 다양하게 지원한다. AI 도 지원하는거 같던데?&#x20;



그래서 Supabase 를 왜 선택했냐면, 우선 개발 속도가 너무 빠르다. NEXT 로 프론트엔드를 구축하고 Vercel 에 배포하고, Supabase 로 DB 를 사용하면, 빠르게 개발 할 수 있다. Supabase 는 PosgreSQ 을 채택했다. 기존의 Firebase 에서 사용하던 NoSQL 이랑 다른 관계형 데이터 베이스이다. 리얼 타임도 지원하는데, PosgreSQL 의 LISTEN/NOTIFY 의 기능을 활용하여 데이터베이스의 변경 사항을 실시간으로 처리할 수 있다. 인증 및 사용자 관리가 굉장히 쉽게 구현되어있으며, Row Level Security 를 활용한 세밀한 접근 제어도 구현할 수 있다. Supabase 는 테이블을 만들게 되면 RESTful API 가 자동으로 생성되고, 또한 GraphQL API 도 지원해서 데이터를 조회하고 조작할 수 있다. Edge Function(서버리스 함수)를 제공하며 특정 트리거에 따른 코드를 실행할 수 있다. Supabase 는 또한 현재 활발한 오픈소스 커뮤니티를 통해 빠르게 성장중이다.

