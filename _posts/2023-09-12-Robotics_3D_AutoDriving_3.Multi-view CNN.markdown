---
layout: post
title:  "Robotics_3D_AutoDriving_3.Multi-view CNN"
date:   2023-09-12 5:18:15 +0700
categories: [Robotics]
use_math: true
---

컴퓨터 비전 심화과정 박충현 패스트캠퍼스 3D 강의 참고 정리

---

> Multi-view CNN

#### Multi-view CNN : Virtual Images

가상의 카메라를 만들고 RGB-D 이미지를 만들고

각각의 2D 이미지를 CNN의 Input으로 넣어 Image feature들을 뽑게 됨

View Pooling 을 사용해 각각의 View를 하나의 Feature로 사용(symmetric)

이를 다시 CNN을 통해 Softmax를 취함

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/3.1.png)

- 장점 : 2D CNN으로 처리가능
- 단점 : 여러가지 View Point들 작업 필요(View Point 가 적으면 문제가 됨)

---

#### 3D CNN

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/3.2.png)

일반적으로 3D Data는 정규화가 되어 있지 않아 Voxelization을 통해 regular한 3D grid를 이용해 공간 전체에서 어느 영역에 차지하고 있는지 표현하고 있는 방식

이후 axies 하나만 추가해서 conv 연산을 수행함

4D 커널을 사용해야함

3D Volum이 커널 갯수 만큼 증가하기 때문에

Input Resolution에 따라 Complexity 가 매우 높아질 수 있음

---

#### Quantization Artifacts

Polygon Mesh를 Voxel로 표현하면 정보 손실이 있음

- Polygon Mesh -> Occupancy Grid 30 x 30 x 30

---

#### Sparsity of 3D Data

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/3.3.png)

Resolution이 커질 수 록 Occupancy 는 줄어듬

128에 대해서 데이터가 없는 공간을 연산하니까 비효율적

---

##### Store only the Occupied Grids

-  따라서 차지하는 영역의 Voxel만 저장해 메모리, 연산에 효율적임
-  surface 부분만 저장 및 연산

---

##### Octree : Recursively Partition the Space

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/3.4.png)

- 3D 공간을 Recursively Partition 한 방법
- 3D 를 쪼개면 8개의 Node 가 생기고 차지하는 영역 만 더 tree 확장
- O-CNN을 사용하면 Resolution 증가해도 GPU Memory 가 감소할 수 있음(차지하는 영역만 저장하므로)
