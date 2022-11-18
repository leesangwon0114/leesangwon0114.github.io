---
layout: post
title:  "HumanPoseEstimation_9.&nbsp;3D_Model-based 접근법"
date:   2022-11-17 10:18:15 +0700
categories: [HumanPoseEstimation]
use_math: true
---

Meta 연구원 문경식의 단일 이미지 인식을 통한 Human Pose Estimation 참고 정리

---

> Model-Based 접근법

In-the-Wild Image <-> Regressor <-> $\theta , \beta$ <-> 3D Human Mdoel <-> 3D Mesh

- Regressor를 통해 SMPL body model parameter를 추정
- 추정된 parameter들은 3D Human Model에 forward되고 3D Mesh를 출력
- 3D Human Model은 학습/테스트 중 고정됨

---

> HMR(2018)

[HMR 논문](https://openaccess.thecvf.com/content_cvpr_2018/papers/Kanazawa_End-to-End_Recovery_of_CVPR_2018_paper.pdf)

---

#### HMR(Human Mesh Recovery)

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/9.1.png)

- 가장 초기 3D Human Pose Estimation 방법 중 하나
- 추정된 3D Mesh를 Orthogonal Projection을 통해 2D로 Project한 후, reprojection loss를 적용
- 2D supervision으로 인해 발생하는 depth ambiguity를 adversarial loss로 해결(이 부분이 핵심)

adversarial loss를 제거하면 2D Supervision을 minimize하는 3D은 무수히 많기 때문에 해부학적으로 불가능한 또는 이미지가 많지 않은 3D Pose가 출력됨

요즘은 adversarial loss를 거의 사용하지 않고 L2 Regularizer를 사용하거나 in-the wild dataset의 3D pseudo-GT를 미리 얻어놓고 그것을 3D supervision용으로 사용

---

> Pose2Pose(2020)

[Pose2Pose 논문](https://arxiv.org/pdf/2011.11534.pdf)

---

#### Pose2Pose

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/9.2.png)

- 3D Positional pose로 부터 3D Rotational Pose를 추정
- 먼저 3D 관절 좌표를 추정(x,y : pixel, z : depth) -> 관절마다 3D Heatmap을 추정하고 soft-argmax를 통해 3D 관절 좌표를 계산
- 각 관절마다 추정된 3D 관절 좌표의 (x,y) 위치에 해당하는 image feature vector를 bilinear interpolation으로 추출
- 추출한 관절마다의 image feature와 추정된 3D 관절 좌표를 concat하여 3D 관절 회전을 추정(joint feat: joint 마다의 contextual information, 3D 관절좌표: geometry information)
- 하나의 fully-connected layer가 3D 관절 회전을 추정함

---

> HybrIK(2020)

[HybrIK 논문](https://openaccess.thecvf.com/content/CVPR2021/papers/Li_HybrIK_A_Hybrid_Analytical-Neural_Inverse_Kinematics_Solution_for_3D_Human_CVPR_2021_paper.pdf)

---

#### HybrIK

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/9.3.png)

##### Forward Kinematics(FK)
- 입력 : 3D 관절 회전(3D relative rotations)과 T-pose 상에서 3D 관절 좌표
- 출력 : 입력의 3D관절 회전을 적용한 3D 관절 좌표
- Recursive 하게 Matrix 곱을 계산하는 것임

##### Inverse Kinematics(IK)
- 입력 : 목표로 하는3D 관절 좌표와 T-pose 상에서 3D 관절 좌표
- 출력 : 입력의 3D관절 좌표에 해당하는 3D 관절 회전
- Ill-posed problem(solution이 없거나 무한히 많을 수 있음)

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/9.4.png)

- 3D rotation을 뉴럴넷으로 직접 추정하는 것은 어렵기 때문에 3D 관절 좌표와 twist 1D rotation만 추정
- 제안하는 HybrIk를 이용해 3D rotation 추정 가능
- 3D 관절 회전을 swing rotation 과 twist rotation으로 분해
- swing rotation은 3D 관절 좌표로 부터 계산 가능하고 twist는 뉴럴넷을 통해 추정

Naive HybrIK는 Twist and Swing 분해에 기반하여 목표 3D 관절 좌표(P), T-pose 3D 관절 좌표(T), 그리고 추정된 twist angle(Phi)로 부터 3D 상대 관절 회전(R)을 얻는 모듈

- 3D 상대 회전으로 바꾸면 twist의 범위가 매우 제한되어 뉴럴넷으로 추정하기 쉬워짐
- SMPL Body Model은 3D 상태 회전을 입력으로 받음

Adaptive HybrIK는 Naive HybrIK에서 추정된 3D 관절 좌표에서 bone length가 T-Pose template에서의 bone length 가 같다고 가정하지만 이 보장이 없어 error accumulation 현상이 일어날 수 있음
-> 이를 방지하고자 target 을 추정된 3D 관절 좌표로 고정 시키지 않고 adaptive 하게 업데이트함

##### 한계점
- 3D 관절 좌표 추정을 위해 3D Heatmap을 추정하고 soft-argmax 로 좌표를 추출
- x, y가 pixel에 정의 되어 있기 때문에 잘려진 사람 부분은 복원 불가
- 잘린부분의 mesh가 맨 밑에 발이 나오는 현상이 발생함

---
