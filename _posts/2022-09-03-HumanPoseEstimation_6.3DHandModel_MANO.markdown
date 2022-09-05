---
layout: post
title:  "HumanPoseEstimation_6.&nbsp;3DHandModel_MANO"
date:   2022-09-03 08:18:15 +0700
categories: [HumanPoseEstimation]
use_math: true
---

Meta 연구원 문경식의 단일 이미지 인식을 통한 Human Pose Estimation 참고 정리

---

> 3D Human Hand Model - MANO

#### MANO

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/6.1.png)

- 위에가 Scan데이터고 아래가 MANO로 모델링한 결과
- 다양한 hand pose와 shape 커버 가능한 모델
- 3D Hand Pose에서 Pose-dependent corrective는 주먹진 상태의 체형 같은 것을 잘 학습해야함

---

#### 3D Scan Datasets for MANO

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/6.2.png)

- Hand 포즈에서 사람 손은 물체와 상호작용을 많이 하기 때문에 초록색과 같은 물체를 통한 데이터들 존재

---

#### PCA Shape and Pose Space of MANO

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/6.3.png)

- PCA space 안에서 만 관절이 정의가 되도록 Pose 에도 PCA 적용


https://github.com/vchoutas/smplx/blob/master/smplx/body_models.py

- 보통 use_pca, flat_hand_mean false로 함(body와 이 두가지 변수 차이 빼고 거의 같음)

---