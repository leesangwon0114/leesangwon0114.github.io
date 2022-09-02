---
layout: post
title:  "HumanPoseEstimation_5.&nbsp;3DHumanPoseEstimation의 기초_3DBodyModel_SMPL"
date:   2022-09-03 07:18:15 +0700
categories: [HumanPoseEstimation]
use_math: true
---

Meta 연구원 문경식의 단일 이미지 인식을 통한 Human Pose Estimation 참고 정리

---

> 3D Human Body Model - SMPL

#### 2D vs 3D

- 임의의 시점(View)으로 대상을 표현할 수 없으면 2D, 있으면 3D
- 오브젝트가 2D 면 앞모습 사람 이미지에서 뒷모습을 볼 수 없지만 3D 는 볼 수 있음
- 3D 는 z 축이 있어 어느 시점에서도 대상을 표현 가능

---

#### 2D Human Pose vs 3D Human Pose

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/4.1.png)

- 다른 뷰에서 포즈를 볼 수 있음

---

#### 3D 관절 좌표 vs 3D 관절 회전

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/4.2.png)

- 왼쪽 3D 관절좌표(3D표현(surface, mesh) 표현 불가)
- 오른쪽 3D 관절 회전(3D표현(surface, mesh) 표현 가능)

---

#### 3D Human Mesh

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/4.3.png)

- 많은 수의 작은 삼각형의 모음으로 3D 물체를 표현
- 꼭지점(Vertex)과 면(face)로 구성
- 다른 3D 물체 표현 법인 volume, point cloud에 비해 효율적이고 직관적이라 많이 쓰임
- 꼭지점의 개수와 각 면을 이루는 꼭지점들의 index는 상수(Mesh Topology)라고 가정하고 꼭지점들의 3D좌표를 구하는 것이 목표(3D Human Mesh Estimation)

Pose과 관절의 회전이고 Shape이 관절의 길이/체형이라고 생각하면됨

3D 관절 회전 + 3D 길이/체형 = 3D Human Mesh

---

#### 3D Human Model

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/4.4.png)

- 3D 관절 회전과 다른 파라미터(eg. 길이 및 체형)으로 부터 3D 인간 메쉬를 출력하는 함수

---

#### 3D 관절 회전

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/4.5.png)

- 부모 관절에 상대적인 3D 회전
- 회전으로 인해 모든 자식 관절들이 이동
- left node에 해당하는 관절들은 3D 회전이 정의 되어 있지 않음
- root node에 해당하는 관절의 3D 회전은 global rotation에 해당
- 즉 pelvis는 root node의 회전임

---

#### 3D 길이 및 체형

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/4.6.png)

- T-pose를 취한 사람의 길이와 체형
- PCA를 통해 사람의 체형과 길이에 대한 latent space 를 모델
- PCA의 Coefficient를 beta로 사용

T-Pose를 취한 3D 스캔 데이터가 있을 때 PCA 알고리즘을 돌림

PCA를 통해 얻은 PCA Components들은 사람 체형들을 구분하는ㄴ 가장 주된 기준들을 나타냄

PCA Components에 beta를 곱한 뒤 더해서 최종 T-Posed 3D Mesh를 얻음

---

#### 3D Human Model

위의 설명을 종합해서 다시 모델을 보면

3D 관절 회전(JX3)이 있고 3D 길이 및 체형은 PCA 를 통해 T-Posed 3D Mesh(VX3)을 얻음

3D 관절 회전 과 T-Posed 3D Mesh를 skinning function에 넣으면 최종 3D Mesh(VX3)을 얻을 수 있음

---

#### skinning function

- 관절들의 위치로 부터 피부를 알아내는 함수
- 가장 많이 쓰이는 함수가 Linear Blend Skinning(LBS) : 모든 관절으 ㅣ변형을 선형으로 합쳐서 3D Mesh를 얻는 skinning algorithm
- Skinning weight(VXJ): 각 mesh vertex 마다 각 관절에 영향을 받은 정도를 나타냄
- 예를 들어 손가락 뿌리부분의 mesh vertex들은 손가락 뿌리의 회전에 가장 크게 영향을 받음
- 3D Human model마다 3D artist 가 미리 만들어 놓는 weight

---

#### 3D Human Model

위의 설명을 종합해서 또 다시 모델을 보면

3D 관절 회전(JX3)이 있고 3D 길이 및 체형은 PCA 를 통해 T-Posed 3D Mesh(VX3)을 얻음

3D 관절 회전 과 T-Posed 3D Mesh를 skinning function LBS에 넣으면 최종 3D Mesh(VX3)을 얻을 수 있음

3D 관절 회전을 통해 앞으로 숙인 것도 표현하기위해 Pose-dependent correctives 같은 기술도 있음

- 3D Human Model을 만들기 위해서는?
- 3D 관절 회전이 정의되기 위한 human kinematic chain
- PCA를 통해 3D 길이 및 체형 Space를 모델: 여러 체형과 키를 가진 사람의 3D Scan이 필요
- Pose-dependent corrective: 여러 포즈를 취한 사람의 3D Scan이 필요
- Skinning weight: 주로 3D artist가 만든 후 약간의 튜닝

3D Human Pose Estimation: 입력 이미지로 부터 3D 관절 회전과 다른 파라미터들을 추정하는 것을 목표

혹성탈출 영화같은 데는 marker 장비를 쓰고 모션 캡쳐(marker-based Mocap)을 하지만 현실에서 어려워 장비가 없는 한장의 이미지로 3D Human Pose Estimation하는 것이 학계의 목표(marker-less Mocap)

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/4.7.png)

---




