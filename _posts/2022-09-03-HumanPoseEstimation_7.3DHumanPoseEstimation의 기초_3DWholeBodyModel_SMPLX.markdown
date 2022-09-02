---
layout: post
title:  "HumanPoseEstimation_7.&nbsp;3DHumanPoseEstimation의 기초_3DWholeBodyModel_SMPLX"
date:   2022-09-03 09:18:15 +0700
categories: [HumanPoseEstimation]
use_math: true
---

Meta 연구원 문경식의 단일 이미지 인식을 통한 Human Pose Estimation 참고 정리

---

> 3D Whole Body Model - SMPLX

앞에는 얼굴과 손이 의미없음

SMPL에 손이랑 얼굴까지 하는 것이 Whole Body모델이고 SMPLX라 함


#### SMPLX

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/7.1.png)

- SMPL(body), MANO(hand), FLAME(face) 3D Human Model들을 하나의 Model로 합친 모델
- MANO와 FLAME와 호환가능해서 그것들의 Pose and Facial Expression 파라미터를 그대로 사용 가능
- SMPL은 조금 다르게 나옴

---

#### Fitting SMPL-X To 2D Pose: SMPLify-X

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/7.2.png)


- 단순히 3가지 모델을 학습시킨데 그치지 않음
- 2D Pose 를 추정 후 여기에 energy minimizationㅇ르 통하여 SMPL-X 파라미터들을 fitting 시키는 프레임워크를 제시
- Energy 는 2D Pose data term과 여러 regularizer로 이루어져 있음
- 2D Pose만으로 하기에는 depth ambiguity에 취약하다는 단점이 있어 regularizer 가 필요함
- 위의 사진에 첫번째 다리가 여러가지 있음(한계점이 많아 많이 쓰이지는 않음)
- Ground Truth가 필요 없는 장점이 있음
- MANO의 PCA Pose Space처럼 VAE 기반의 Vposer를 개발하여 3D 관절 회전을 normal Gaussian space에 임베딩 시켰음(해부학적으로 불가능한 Pose는 방지하는 효과)
- 반대로 가능한 body pose 셋을 줄여놔서 학습데이터에 없는 포즈를 추정하기는 어려움

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/7.3.png)

https://github.com/vchoutas/smplx/blob/master/smplx/body_models.py

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/7.4.png)

https://github.com/vchoutas/smplify-x

- fit_single_frame.py 의 camera_init : 2d Pose 랑 가깝게 fitting 하기 위해 Loss 추정

---
