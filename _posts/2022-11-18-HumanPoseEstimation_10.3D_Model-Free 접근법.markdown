---
layout: post
title:  "HumanPoseEstimation_10.&nbsp;3D_Model-Free 접근법"
date:   2022-11-18 1:18:15 +0700
categories: [HumanPoseEstimation]
use_math: true
---

Meta 연구원 문경식의 단일 이미지 인식을 통한 Human Pose Estimation 참고 정리

---

> Model-Free 접근법

In-the-Wild Image <-> Regressor <-> 3D Mesh

- Regressor를 통해 Human Mesh 의 3D 좌표를 직접 추정
- 추정된 mesh의 topology(vertex개수, face를 이루는 vertex index)는 SMPL의 topology를 사용

##### Voxel기반의 3D 관절 좌표 추정 기법

- 가장 높은 성능을 내는 3D관절 좌표 추정 기법들은 주로 voxel기반의 3D Heatmapㅇ르 추정해서 높은 성눙을 거둠
- 3D Heatmap은 입력 이미지의 x,y 축을 보존하기 때문에 pixel간의 위치관계를 사용할 수 있음
- 3D Heatmap은 voxel마다 likelihood를 추정하기 때문에 uncertainty를 model할 수 있음
- 관절만 하는 애들은 관절마다 3D Heatmap을 만들어 3D Pose를 추정
- Mesh로의 확장은 쉽지않음 -> voxel은 메모리를 매우 많이 차지

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/10.1.png)

---

> I2L-MeshNet(2020)

[I2L-MeshNet 논문](https://www.ecva.net/papers/eccv_2020/papers_ECCV/papers/123520732.pdf)

---

#### I2L-MeshNet

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/10.2.png)

- three lixel-based 1D heatmaps(in x-, y- and z-axis)을 mesh vertex 마다 추정(메모리 많이 차지하는 문제를 해결하고자함)
- soft-argmax 를 사용하여 미분 가능한 방식으로 heatmap에서 좌표를 추출
- lixel-based 1D heatmaps의 에러가 가장 낮았음
- 바로 voxel-based 3D heatmap은 Over Flow Memory로 비효율적
- xy pixel hm + z lixel hm 보다도 xyz lixel hm 이 더 효율적인 결과가 나옴

---

> Pose2Mesh(2020)

[Pose2Mesh 논문](https://arxiv.org/pdf/2008.09047.pdf)

---

#### Pose2Mesh(2D Pose-to-3D Mesh Network)

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/10.3.png)

- In the wild Image와 Mocab 데이터(Muti-view studio) 간의 apperance(사람의 geometry)의 도메인 차이를 줄이고자 Input을 2D Human Pose를 이용하고자하는 아이디어
- 입력을 geometry(2d pose)로 하면 도메인 gap을 줄이지 않을까?
- 2D Pose Estimators은 in-the-wild images에서 이미 좋은 선응을 거두고 있으며 annotate 될 수 있음
- 2D Pose Estimators들은 large-scale in the wild 2d pose datasets에 학습 가능

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/10.4.png)

- 입력을 이미지로 하지 않고 2D Pose로 함
- Coarse to fine Mesh Estimation 을 사용해 메모리와 시간을 빠르게 하고 error도 약간 줄임
- Training은 Mocab 데이터에서하고 Test는 In-the-Wild로 했을 때 다른 것들은 에러가 높지만 Pose2Mesh를 에러율이 작음(domain gap을 줄였기 때문에)

---

> METRO(2021)

[METRO 논문](https://arxiv.org/pdf/2012.09760.pdf)

---

#### METRO

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/10.5.png)

- Transformer를 도입한 최초의 3D Human Pose Estimation 방법
- Query: GAP을 적용한 Image Feature Vector
- Positional Encoding: T-Pose의 3D 관절, mesh vertex 좌표들
- Self-attention을 이용해서 vertex와 joint간의 inter-relationship을 모델링
- Transformer 인코더를 이용하여 vertex 마다 3D 좌표를 추정
- 입력 joint / vertex query 중 랜덤으로 선택된 query들을 mask하는 data augmentation의 한 종류
- Transformer가 query 들 간의 shot-and long-range relationship을 더 잘 사용하도록 유도

---