---
description: Yorkie 오픈소스를 분석해보자
cover: >-
  https://images.unsplash.com/photo-1543946602-a0fce8117697?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw2fHxuZXR3b3JrfGVufDB8fHx8MTcyMDg0MTcxMHww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# CRDT 란 무엇인가?



### About [Yorkie ](https://yorkie.dev/docs)

Yorkie is an open-source document store that provides a suite of tools for building real-time collaborative applications like Google Docs and Figma. It includes a JSON-like Document based on Conflict-free Replicated Data Types(CRDTs), Presence, and auth webhook features to enable synchronous co-editing.

Unlike other libraries like AutoMerge and Yjs, Yorkie includes a full-stack solution with SDKs, Cloud or Self-Hosted Server, making it easier to implement co-editing capabilities in your application.

In the following sections, we will explore the structure and workings of Yorkie in more detail.



### Components

Yorkie consists of three main components: Clients, Documents, and Server.

* [Client](https://yorkie.dev/docs/js-sdk#client)s are the entities that communicate with the server. Changes made by documents can be synchronized with the server, allowing for real-time collaboration.
* [Document](https://yorkie.dev/docs/js-sdk#document)s are the core data structures of Yorkie, and are based on Conflict-free Replicated Data Types(CRDTs). This allows for seamless collaboration on a document, even when clients are offline.
* [Server](https://yorkie.dev/docs/self-hosted-server) is the central hub of the Yorkie, receiving changes from clients, storing them in the database, and propagating them to other clients who are subscribed to the same documents.

In addition, [Project](https://yorkie.dev/docs/cli#project)s in Yorkie represent separate services or applications within the system. This allows for multiple independent projects to coexist within a single Yorkie installation.



### How it works

A high-level overview of Yorkie is as follows:

```
 Client "A" (Go)                 Cloud or Self-Hosted Server     MemDB or MongoDB
┌───────────────────┐           ┌────────────────────────┐      ┌───────────┐
│  Document "D-1"   │◄─Changes─►│  Project "p-1"         │      │ Changes   │
│  { a: 1, b: {} }  │           │ ┌───────────────────┐  │◄────►│ Snapshots │
└───────────────────┘           │ │  Document "D-1"   │  │      └───────────┘
 Client "B" (JS)                │ │  { a: 2, b: {} }  │  │
┌───────────────────┐           │ │                   │  │
│  Document "D-1"   │◄─Changes─►│ │  Document "D-2"   │  │
│  { a: 2, b: {} }  │           │ │  { a: 3, b: {} }  │  │
└───────────────────┘           │ └───────────────────┘  │
 Dashboard or CLI               │                        │
┌───────────────────┐           └────────────────────────┘
│  Query "Q-1"      │                 ▲
│  P-1.find({a:2})  ├─────── Query────┘
└───────────────────┘
```

The Yorkie system allows clients to have replicas of a document, representing an application model, on multiple devices. Each client can edit the document independently on their local devices, even when offline. When a network connection is available, Yorkie determines which changes need to be synced between clients and brings their replicas into the same state. In the event that the document is being edited concurrently on different devices, Yorkie automatically syncs the changes and resolves any conflicts, ensuring that all replicas end up in the same state.

To get started with Yorkie, please refer to the [Getting Started](https://yorkie.dev/docs/getting-started).



### CRDT vs OT

CRDT ( Conflict-Free-Replicated Data Types) 와 OT (Operational Transformation) 란 동시 편집 기술로, 실시간 협업 툴에 관련된 기술이다.



### 실시간 협업 에디터가 동작되는 과정



### CRDT 와 OT 비교









