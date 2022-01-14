---
layout: post
title:  "ROS2_TurtleBot3_6.&nbsp;Cartographer_LocalSLAM"
date:   2022-01-14 05:18:15 +0700
categories: [ROS2]
use_math: true
---

Ubuntu 18,04 기반 ROS2 Dashing Diademeta 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> Cartographer

#### Local SLAM

다양한 range data로 부터 필터되고 모아진 scan이 준비되면 Local SLAM알고리즘의 준비가 됨

여기서 pose extrapolator(추정) 부터 초기 추측을 사용하는 scan matching을 통해 현재 submap 구성에 새로운 scan을 삽입함
-> 재 방문 없이 Pose Correction 수행이 목적

pose extrapolator 의 아이디어는 다른 센서의 센서 데이터들 사용하여 submap에 다음 scan이 삽입되어야할 위치를 예측하는 것임

위의 내용을 이해하기 쉽게 RGBD scan matching을 통해 맵을 reconstruction하는 과정을 그림으로 표현

>> RGBD Data Input 1 
 
 ![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.7.png)

>> RGBD Data Input 2

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.8.png)

>> Input1, Input2 사이의 Data 의 scan matching

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.9.png)

>> 두 Input1,2 를 합친 하나의 Submap이 나옴(Output1)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.10.png)

>> 다시 RGBD Data Input 3 이 들어옴

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.11.png)

>> Output 1과 Input 3 과 scan matching 진행

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.12.png)

>> Output1과 Input3 를 합친 하나의 Submap이 나옴(Output2)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.13.png)

>> 위의 과정을 반복하여 submap을 구성해가고 에러를 줄임

Cartographer에서 Scan Matching을 위해 CeresScanMatcher과 RealTimeCorrelativeScanMatcher 두가지 전략이 가능하다.

- CeresScanMatcher
    - 이전의 초기 추측을 가지고 scan match 가 submap에 가장 잘 맞는 위치를 찾음
    - submap과 sub-pixel을 보정하면서 scan을 정렬함
    - 이는 빠르지만 submap의 해상도 보다 큰 에러는 잡지 못함
    만일 센서 setup과 시간이 적당하다면 CeresSCanMatcher 만 사용하는 것이 가장 베스트한 초이스임

- RealTimeCorrelativeScanMatcher
    - 다른센서가 없거나 센서 값을 신뢰하지 못할 때 사용할 수 있음
    - loop closure의 submap scan match 와 비슷한 방식을 사용하지만 현재 submap 기준으로 match를 진행하는 점이 다름
    - 그런 다음 가장 잘 맞는 것이 CeresScanMatcher 의 이전 값으로 사용됨
    - 계산 비용이 비싸지만 computing 능력이 되면 강력한 기능 제공

어느 쪽이든 CeresSCanMatcher은 각 입력에 특정 가중치를 부여할 수 있도록 구성 가능함

이 가중치는 신뢰의 척도이며 static covariance 로 볼 수 있음
(가중치가 클수록 cartographer 에서 가중치가 큰 데이터를 강조하여 scan matching을 진행함)

데이터 설정에는 occupied space(points from the scan), translation과 rotation(from the pose extrapolator) 포함하고 있음

    - TRAJECTORY_BUILDER_3D.ceres_scan_matcher.occupied_space_weight
    - TRAJECTORY_BUILDER_3D.ceres_scan_matcher.occupied_space_weight_0
    - TRAJECTORY_BUILDER_3D.ceres_scan_matcher.occupied_space_weight_1
    - TRAJECTORY_BUILDER_nD.ceres_scan_matcher.translation_weight
    - TRAJECTORY_BUILDER_nD.ceres_scan_matcher.rotation_weight

occupied_space_weight_0 와 occupied_space_weight_1 은 3D에서 각각 고해상도, 저해상도와 연관되어 있음

CeresScanMatcher 은 Ceres Solver라는 라이브러리 이름을 가지며 구글에서 non-liner 최소자승법( http://jinyongjeong.github.io/2017/02/26/lec12_Least_square/) 문제를 해결하기 위해 개발되었음

Scan Matching 문제는 두 스캔 사이의 motion(transformation matrix) 의 오차를 최소화하도록 모델림 됨

descent 알고리즘을 통해 motion을 최적화 하고 수렴 속도를 조정해서 Ceres 를 구성할 수 있음

    - TRAJECTORY_BUILDER_nD.ceres_scan_matcher.ceres_solver_options.use_nonmonotonic_steps
    - TRAJECTORY_BUILDER_nD.ceres_scan_matcher.ceres_solver_options.max_num_iterations
    - TRAJECTORY_BUILDER_nD.ceres_scan_matcher.ceres_solver_options.num_threads

RealTimeCorrelativeScanMatcher는 센서에 대한 신뢰도에 따라 전환할 수 있음

maximum distance radius 와 maximum angle radius 에 의해 정의된 search window 에서 유사한 scan들을 찾는 역할을 함

변환 및 회전 구성 요소에 대해 다른 가중치를 선택할 수 있으며 예를들어 로봇이 많이 회전하지 않는 것을 안다며 무게의 weight 를 증가시킬 수 있음

    - TRAJECTORY_BUILDER_nD.use_online_correlative_scan_matching
    - TRAJECTORY_BUILDER_nD.real_time_correlative_scan_matcher.linear_search_window
    - TRAJECTORY_BUILDER_nD.real_time_correlative_scan_matcher.angular_search_window
    - TRAJECTORY_BUILDER_nD.real_time_correlative_scan_matcher.translation_delta_cost_weight
    - TRAJECTORY_BUILDER_nD.real_time_correlative_scan_matcher.rotation_delta_cost_weight

submaps에 너무 많은 scan들의 삽입을 막기 위해서 motion filter 를 수행함

즉, 특정 거리, 각도 또는 시간 임계값을 넘어갈 때 스캔이 현재 submap에 삽입됨

    - TRAJECTORY_BUILDER_nD.motion_filter.max_time_seconds
    - TRAJECTORY_BUILDER_nD.motion_filter.max_distance_meters
    - TRAJECTORY_BUILDER_nD.motion_filter.max_angle_radians

하나의 submap은 local SLAM이 일정량의 범위 데이터를 수신했을 때 완료된 것을 간주함

    - TRAJECTORY_BUILDER_nD.submaps.num_range_data

submap은 범위 데이터를 두 개의 서로 다른 데이터 구조에 저장할 수 있음

가장 많이 사용하는 것이 probability grdis임

그러나 2D에서 Truncated Signed Distance Fields(TSDF) 도 사용 가능

    - TRAJECTORY_BUILDER_2D.submaps.grid_options_2d.grid_type

Probability grdis 은 2D 또는 3D 표로 공간을 잘라냄

각 확률값은 범위 데이터가 측정되는 곳은 hit, 센서와 측정된 지점 사이의 공간은 misses 에 따라 업데이트 됨

hists, misses 는 다른 weight를 가질 수 있으며 아래 설정 값 사용

    - TRAJECTORY_BUILDER_2D.submaps.range_data_inserter.probability_grid_range_data_inserter.hit_probability
    - TRAJECTORY_BUILDER_2D.submaps.range_data_inserter.probability_grid_range_data_inserter.miss_probability
    - TRAJECTORY_BUILDER_3D.submaps.range_data_inserter.hit_probability
    - TRAJECTORY_BUILDER_3D.submaps.range_data_inserter.miss_probability

2D 에서는 하나의 probability grid가 submap 당 저장되며 3D 에서는 두개의 하이브리드 probability grids 가 사용됨

멀리 측정되는 것은 low 해상도를 가진 hybrid grid, 가까이 측정되는 것은 high 해상도를 가진 hybrid grid

Scan Matching은 저해상도 포인트 클라우드 정렬을 시작으로 고해상도 포인트 클라우드를 정제함

    - TRAJECTORY_BUILDER_2D.submaps.grid_options_2d.resolution
    - TRAJECTORY_BUILDER_3D.submaps.high_resolution
    - TRAJECTORY_BUILDER_3D.submaps.low_resolution
    - TRAJECTORY_BUILDER_3D.high_resolution_adaptive_voxel_filter.max_range
    - TRAJECTORY_BUILDER_3D.low_resolution_adaptive_voxel_filter.max_range
