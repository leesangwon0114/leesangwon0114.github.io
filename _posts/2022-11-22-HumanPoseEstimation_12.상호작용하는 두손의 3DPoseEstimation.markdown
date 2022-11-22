---
layout: post
title:  "HumanPoseEstimation_12.&nbsp;상호작용하는 두손의 3DPoseEstimation"
date:   2022-11-22 4:18:15 +0700
categories: [HumanPoseEstimation]
use_math: true
---

Meta 연구원 문경식의 단일 이미지 인식을 통한 Human Pose Estimation 참고 정리

---

> 상호작용하는 두손의 3D Pose Estimation

#### 단일 손 vs 상호작용하는 두 손의 3D Pose Estimation

##### 3D Hand Pose Estimation

- 입력 이미지로 부터 손의 관절을 3D 공간에서 위치화
- 손가락 마다 4개의 관절 + 손목 1개의 관절 -> 21개의 관절

3D Interacting Hand Pose Estimation으로 확장

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/12.1.png)

##### 기존 3D Hand Pose Datasets

- Depth map 과 RGB 기반을 포함한 대부분의 데이터 셋 들이 single hand만 포함
- 손 끼리의 interaction은 분석하기 어려워 데이터 셋이 발표되지 못했음

<span style='background-color: #fff5b1'>HANDS 2017 dataset, RHP dataset, FreiHAND dataset</span>

몇몇 dataset(RHP, Tzionas et al, Mueller et al. Simon et al) 이 있지만 단점들이 존재

---

> InterHand2.6M(2020)

처음으로 제안된 큰 규모의 real RGB와 정확한 GT 3D Pose를 포함한 3D Interacting Hand Pose Dataset

[InterHand2.6M 논문](https://www.ecva.net/papers/eccv_2020/papers_ECCV/papers/123650545.pdf)

---

#### 3D Interacting Hand Pose Estimation의 어려움

- 매우 비슷하게 생긴 양 손의 손가락들 <span style='color: red'>self-similarity</span>
- 매우 심한 가려짐 <span style='color: red'>occlusions</span>
- 복잡한 손 관절의 움직임 <span style='color: red'>complicated articulations</span>

---

#### InterHand2.6M; First Large-Scale and Real Captured 3D Interacting Hand Dataset

- 페이스북 Capture Studio에서 촬영
- 100개의 카메라 90fps로 4K 해상도 촬용
- 한 사람의 왼손, 오른손을 다룸

##### Hand Sequences

- Peak Pose : 빈번히 사용되는 다양한 sign languages, 최대한의 손가락 굽힘으로 된  극단적인 손 자세들
- Range of motion : 친구가 올 때 인사하는 제스쳐 등 자유로운 포즈

##### Semi-Automatic Annotation

마커를 사용하지 않고 Annotation Tool 사용해 반자동으로 진행

- Muti-view consistency 를 위해 5-6 view를 annotator에게 동시에 보여줌
- 2개 이상의 view에 annotation이 완료되면 자동으로 triangulate을 해서 3D space로 올리고 다른 view로 project

Annotation Tool이 있지만 모두 수동으로 하기 어려움

Automatic Machine Annotation을 통해 당시 높은 성능을 하진 2D Keypoint Detctor를 학습해 남은 모든 이미지에 inference하고 RANSAC을 이용한 triangulation을 통해 3D 좌표 획득

---

#### InterNet: 3D Interacitng Hand Pose Estimation Network

base 네트워크

![Alt text](http://leesangwon0114.github.io/static/img/HumanPoseEstimation/12.2.png)

---