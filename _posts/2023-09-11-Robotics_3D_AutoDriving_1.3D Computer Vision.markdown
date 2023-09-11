---
layout: post
title:  "Robotics_3D_AutoDriving_1.3D Computer Vision"
date:   2023-09-11 4:18:15 +0700
categories: [Robotics]
use_math: true
---

컴퓨터 비전 심화과정 박충현 패스트캠퍼스 3D 강의 참고 정리

---

> 3D Data

#### Representation of 3D Data

Multi-view RGB(D) Images

Volumetric (정육면체 형식으로 표현)

Polygonal Mesh (삼각형, 다각형으로 3차원 물체 표면을 표시)

Primitive-based CAD models

Point Cloud (3차원 점의 집합을 점 구름으로 표현하는 방식)

Point Cloud를 많이 쓰며 이를 센싱할 수 있는 센서는 RGB-D Camera, 3D LiDAR(Light Detection and Ranging) 센서가 있음

##### 2D vs 3D

||Image|3D geometry|
|---|---|---|
|Boundary|Fixed|Varying|
|Siganl|Dense|Very sparse in 3D domain|
|Convolution|Well-defined|Questionable|

- 3D는 Boundary 가 제한되어 있지 않고 다양함
- 3D는 정의되어 있는 3차원 공간에 모든 Signal이 있는게 아니고 표면 부분만 존재
- 3D는 Convolution 을 잘 정의하는 것이 연구가 될만큼 챌린저블함

---

##### Properties of Point Sets

1. Unordered : Point Cloud 는 순서가 없는 포인트의 집합임
2. Interaction among points : 근처 포인트들의 local 구조를 찾는 것이 모델에 필요함
3. Invariance under transformations : 회전과 변형에서 Invariance 함을 만족하게 해야함(자동차가 뒤집혀져 있더라도 자동차는 자동차)

---

