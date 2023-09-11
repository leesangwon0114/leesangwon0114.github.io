---
layout: post
title:  "Robotics_3D_AutoDriving_2.PointNet"
date:   2023-09-11 5:18:15 +0700
categories: [Robotics]
use_math: true
---

컴퓨터 비전 심화과정 박충현 패스트캠퍼스 3D 강의 참고 정리

---

> PointNet

#### Datasets for 3D Objects

- ShapeNet 이라는 Large-scale의 3D 데이터 셋

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.1.png)

- PartNet Segmantion 까지의 데이터 셋

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.2.png)

- SceneNet : 가상으로 Object Scenes 

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.3.png)

- ScanNet : 아이폰 같은 카메라를 통해 실제 데이터 셋의 Scenes을 수집한 데이터 셋으로 현재 가장 많이 쓰이는 데이터셋

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.4.png)

#### Datasets for Outdoor 3D Scenes

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.5.png)

---

#### 3차원 처리 기본 네트워크 : Point Network

Point Cloud를 그대로 받아서 Obejct Classification, Segmentation을 함

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.6.png)

---

##### Architecture

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.7.png)

- Shared MLP : 하나의 MLP를 가지고 각각의 Point를 동일한 MLP를 이용해서 처리(Point-wise 연산이며 각각 독립 연산임)

---

##### Permutation Invariance

Point sets는 순서가 존재하지 않음

이를 위해 Symmetric Function 개념이 필요함

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.8.png)

h라는 함수가 각각의 독립적으로 수행된다면 나중에 output으로 나오는 함수가 symmetric이면 전체가 symmetric 함

input 노드가 순서가 바뀌어도 g 라는 output은 똑같음을 의미(간단하게 sum같은 것을 할 수 있음)

마지막으로 r를 통과하면 output을 얻게됨

이를 위한 여러 네트워크를 실험함

---

##### Approaches to achieve order invariance

1) LSTM : accuracy 78.5

2) MLP (unsorted input) : accuracy 24.2

3) MLP (sorted input) : accuracy 45.0

4) Attention sum : accuracy 83.0

5) Average Pooling : accuracy 83.8

6) Max Pooling : accuracy 87.1

따라서 Max Pooling 사용하고 global feature을 뽑음

---

##### Transformation matrix

Transformation 에 대해서도 invariance 해야함

90도 회전했다가 다시 -90도 회전하면 자기 자신이니까 regularization term을 추가해서 만든 트릭

![Alt text](http://leesangwon0114.github.io/static/img/Robotics/2.9.png)

---

##### Segmentation Network

각 point 마다 특징을 예측해야 해서 n x 64에 global feature를 concat 해서 다시 특징을 뽑음

---
