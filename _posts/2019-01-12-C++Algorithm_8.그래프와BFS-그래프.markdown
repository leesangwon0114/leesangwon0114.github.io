---
layout: post
title:  "C++Algorithm 8.&nbsp;그래프와BFS - 그래프"
date:   2019-01-12 01:18:15 +0700
categories: [Algorithm, c++]
---

codeplus 백준 강사 강의 내용기반으로 정리한 내용입니다.(비공개 포스트)

---

> 그래프

Node 와 Edge

Edge - 정점간의 관계를 나타냄

Path - 정점에서 다른 정점으로 가는 경로

#### 인접행렬 vs 인접리스트

O(V^2) vs O(E)

인접행렬은 u, v에 대해 u->v에 간선이 존재하는지 확인이 빠름(O(1))

인접행렬은 반대 간선 확인 역시 O(1), (u,v), (v,u) 역방향 간선하는지 존재 확인

단, 공간이 너무 많이 필요함

-> 인접리스트로 많이 사용함
