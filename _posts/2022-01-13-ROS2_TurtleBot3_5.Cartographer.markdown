---
layout: post
title:  "ROS2_TurtleBot3_5.&nbsp;Cartographer"
date:   2022-01-13 05:18:15 +0700
categories: [ROS2]
use_math: true
---

Ubuntu 18,04 기반 ROS2 Dashing Diademeta 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> Cartographer

2D/3D Lidar Graph SLAM 기반 알고리즘

SLAM 종류에는 Filtering 방식의 EKF, Particle 알고리즘과 Smoothing 기반의 Pose Graph Optimization 이 있음

위의 알고리즘들을 통해 현실세계의 노이즈 에러를 줄임

- 이상적인 상황의 SLAM

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.1.png) ![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.2.png)

    - Lidar 와 Odometry 의 에러가 없으면 맵을 잘 생성함

- Lidar는 정확하지만 odometry에 노이즈가 있는 상황의 SLAM

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.3.png) ![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.4.png)

    - 초록색 모바일 로봇이 실제 Pose이며 회색이 추정하는 Robot Pose
    - 주행계의 노이즈로 위와 같이 맵 생성 시 에러 발생
    - 이를 SLAM 알고리즘을 통해 조정함
    - Cartographer는 Backend(Global SLAM)에서 Pose Graph Optimization 방식을 이용 
    - cf) https://www.youtube.com/watch?v=saVZtgPyyJQ

- Cartographer 은 크게 Local SLAM 과 Gloal SLAM으로 나뉨(자세한 사항은 아래 설명)

> Cartoprapher Paper

https://research.google/pubs/pub45466/

---

#### Cartographer 구조

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.6.png)

---

#### Input Sensor Data

2D or 3D Lidar 센서를 통해 다양한 방향에서의 depth 정보를 제공 받음

- 그러나 현실 세계에서는 센서의 부분적인 먼지, 거리가 먼 측정 값의 반사, 센서 노이즈 등으로 SLAM에 노이즈가 발생함
- 위의 이슈로 인해 Cartographer는 bandpass filter(대역통과필터)를 적용하여 최소 및 최대 범위 사이의 값만 유지(로봇과 센서에 따라 min, max 값이 결정됨)
- Cartographer ROS에서는 해당 값은 아래 설정으로 가능
    - TRAJECTORY_BUILDER_nD.min_range
    - TRAJECTORY_BUILDER_nD.max_range
- 2D 에서는 max_range 보다 더 먼 범위는 TRAJECTORY_BUILDER_2D.missing_data_ray_length 값으로 대체함
- Cartographer Configuration 파일에서 모든 distance 는 미터(meters)로 정의됨
- 거리는 로봇이 실제로 움직이는 동안 측정되는데 배치 단위로 센서가 ROS메시지로 부터 전달 됨
- 측정 값을 자주 받을 수록 하나의 일관성있는 스캔을 모으는데 유리함
- 그러므로 range data를 최대한 많이 제공 받을 것을 권장하며 해당 값은 아래로 설정 가능
    - TRAJECTORY_BUILDER_nD.num_accumulated_range_data
- range data는 하나의 점을 측정하지만 각도가 다양함
- 멀리있는 물체일수록 레이저 hit 가 적고 더 적은 points 를 받게됨
- point clouds의 계산 가중치를 줄이기 위해 subsample을 하여 사용하지만 랜덤 적으로 샘플링 할 경우 적은 point의 point들을 줄여 density 문제를 야기할 수 있음
- 따라서 raw 포인트들을 일정한 크기의 cube로 다운샘플링하고 각 cube의 중심만 유지하는 voxel(3D 화소) 필터를 사용함
- cube 사이즈가 작을 수록 dense 한 데이터로 표현되고 계산량도 많아짐
- cube 사이즈가 클수록 data 손실이 발생할 것이며 이 사이즈는 아래 값으로 설정 가능함
    - TRAJECTORY_BUILDER_nD.voxel_filter_size
- 고정된 크기의 voxel 필터를 적용 후 adaptive voxel 필터도 적용함
- 아래 max_length 값 언더로 optimal 한 voxel 사이즈를 결정
    - TRAJECTORY_BUILDER_nD.*adaptive_voxel_filter.max_length
    - TRAJECTORY_BUILDER_nD.*adaptive_voxel_filter.min_num_points
- IMU(Inertial Measurement Unit, 관성측정장치) 는 중력의 정확한 방향과 노이즈를 제공하지만 전반적으로 로봇의 회전을 잘 표시해 SLAM에 유용한 정보를 제공함
- IMU 노이즈를 거르기 위하여 특정 시간을 관찰함
- 2D SLAM에서는 range data 가 추가적인 정보 없이 다루루 수 있으며 Cartographer 에서 IMU를 사용할지 말지 결정할 수 있음
- 3D SLAM에서는 scan matching 의 복잡도를 줄이기 위해 scan 방향의 초기 추측에 IMU가 사용되므로 필수로 필요함
    - TRAJECTORY_BUILDER_2D.use_imu_data
    - TRAJECTORY_BUILDER_nD.imu_gravity_time_constant
- Cartographer configuration 파일에서 모든 시간은 초 단위임

---
