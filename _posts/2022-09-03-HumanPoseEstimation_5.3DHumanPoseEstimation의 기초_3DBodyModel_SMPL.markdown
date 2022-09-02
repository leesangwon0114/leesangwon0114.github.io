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

#### SMPL

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/5.1.png)

- Bs를 보면 체형이 뚱뚱해짐
- Bp를 보면 어깨가 올라가고 골반이 많이 바뀐 것을 볼 수 있음(자세를 취하면서, 다리를 들어올리면서 체형 변화가 더해짐)
- W() skining weight까지 더해저 최종적인 모델이 나옴

---

#### 3D Scan Datasets for SMPL

- MultiPose dataset : pose-dependent corrective 학습
- MultiShape Dataset: beta를 얻음

https://github.com/vchoutas/smplx/blob/master/smplx/body_models.py

