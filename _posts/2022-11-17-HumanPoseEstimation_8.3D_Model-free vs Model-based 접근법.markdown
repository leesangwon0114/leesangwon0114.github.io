---
layout: post
title:  "HumanPoseEstimation_8.&nbsp;3D_Model-free vs Model-based 접근법"
date:   2022-11-17 09:18:15 +0700
categories: [HumanPoseEstimation]
use_math: true
---

Meta 연구원 문경식의 단일 이미지 인식을 통한 Human Pose Estimation 참고 정리

---

> Model-Based vs Model-Free 접근법


#### Model-based 접근법

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/8.1.png)


- 입력 이미지로 부터 3D Human Model Parameter를 추정
- 추정된 Parameter들을 3D 모델에 forward 하여 최종 3D human mesh를 얻음
- 3D 관절 회전을 통해 3D Mesh를 얻는 과정은 Human Kinematic Chain을 따라 3D Relative 회전이 누적되어 적용되어 부모 joint의 rotation 에러가 자식 joint에 영향을 끼칠 수 있음
- 이러한 Error Accumulation 현상 때문에 직접적으로 좌표를 추정하는 <span style='background-color: #fff5b1'> model-free에 비해 정확도가 낮을 수 있음 </span>
- <span style='background-color: #fff5b1'>하지만 3D 관절 회전은 다른 캐릭터로의 포즈 전송을 가능하게 하는 등 유용하게 쓸 수 있음($\theta$)</span>
- 실제로는 추정한 mesh는 의미 없으며 $\theta$ 를 가지고 나의 포즈가 다른 캐릭터 포지가 흉내 낸다던지 등 응용에 쓰임

---

#### Model-Free 접근법

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/8.2.png)


- 입력 이미지로 부터 3D Human Mesh Vertex들의 좌표를 추정
- 이때 3D Human Mesh은 3D 모델의 topology와 같다고 가정
- 진정한 모델 Free는 topology가 없는 데이터를 추정해야함
- SMPL은 6980개의 Vertex로 이루어져 이 개수 만큼 모두 3D좌표를 추정해야함(6980 x 3)
-  Body 관절이 보통 20개 전후로 정의되는 반면 Vertex는 매우 갯수가 많음(Computational OverHead)
- 따라서 mesh vertex들이 smooth하지 않을 수 있음
- 3D 관절 회전 값을 얻지 못해 다른 Application에 적용하기에는 제약이 있음
- 정확도는 Model-based 보다 높을 수 있음

---

