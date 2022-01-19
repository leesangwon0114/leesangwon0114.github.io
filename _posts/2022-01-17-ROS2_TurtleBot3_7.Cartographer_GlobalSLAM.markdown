---
layout: post
title:  "ROS2_TurtleBot3_7.&nbsp;Cartographer_GlobalSLAM"
date:   2022-01-17 05:18:15 +0700
categories: [ROS2]
use_math: true
---

Ubuntu 20,04 기반 ROS2 Foxy 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> Cartographer Global SLAM

#### Global SLAM

local SLAM이 성공적인 submap들을 생성해 내는 동안 global 최적화 작업이 백그라운드에서 진행됨

일관된 글로벌 map 생성을 위해 submap 간 재정렬을 하는 역할을 담당

예를들어 이 최적화는 loop closures와 관련하여 submap을 적절하게 정렬하기 위해 현재 구축된 궤적을 변경하는 것을 담당함

이 최적화는 특정수의 궤적 노드들이 삽입되면 일괄적으로 시행되며 얼마나 자주 진행할지는 아래 설정으로 조절 가능함

    - POSE_GRAPH.optimize_every_n_nodes

-> 0으로 설정 시 global SLAM을 비활성화 시키고 local SLAM에 집중해서 SLAM을 진행할 수 있음

global SLAM은 "GraphSLAM"의 한 종류이며 pose graph optimization을 수행함

pose graph optimization은 노드와 submapㄱ간의 제약을 만들고 제약된 그래프를 최적화 함

여기서 제약은 직관적으로 모든 노드를 묶는 작은 로프라 생삭할 수 있음

밀도가 희박한 pose는 해당 로프를 통해 빠르게 조정되고 그 결과로 나온 net을 pose graph라 함

>> Note \
>> 제약은 Rviz에서 시각화 가능하며 global SLAM에서 조율하는데 편리함 \
>> POSE_GRAPH.constraint_builder.log_matches 를 켰다 껐다하면서 histogram 형태의 제약들을 정기적인 report를 얻을 수 있음

- Non-global 제약은 궤도에서 서로 밀접하게 따르는 노드 간에 자동으로 구축됨
- Global 제약은 새로운 submap과 공간(search window의 부분)안에 "close enough"한 노드와 스캔매칭시 가장 잘 맞는 이전 노드 간에 정기적으로 검색됨
- 직관적으로 글로벌 로프는 구조에 매듭을 만들고 두 가닥을 단단히 가깝게 해줌

    - POSE_GRAPH.constraint_builder.max_constraint_distance
    - POSE_GRAPH.fast_correlative_scan_matcher.linear_search_window
    - POSE_GRAPH.fast_correlative_scan_matcher_3d.linear_xy_search_window
    - POSE_GRAPH.fast_correlative_scan_matcher_3d.linear_z_search_window
    - POSE_GRAPH.fast_correlative_scan_matcher*.angular_search_window

제약의 양을 제한하기위해 Cartographer는 제약을 구축하는데 있어 가까운 모든 노드들의 subsample 집합만 오려함

이는 샘플 제어 상수에 의해 조절됨

    - POSE_GRAPH.constraint_builder.sampling_ratio

너무 적은 수의 노드를 샘플링하면 제약 조건이 누락되고 loop closures 가 비효율적일 수 있음

너무 많은 노드를 샘플링하면 global SLAM 속도가 느려지고 실시간 loop closures를 막음

노드와 submap이 제약을 만들때 FastCorrelativeScanMatcher 라는 첫번째 scan matcher를 거침

이 scan matcher는 loop closures를 실시간에 가깝도록 Cartographer에서 디자인 되었음
(실제 논문에서 제목 부터 Real-Time Loop Closure in 2D LIDAR SLAM 임)

[Real-Time Loop Closure in 2D LIDAR SLAM 논문 링크](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/45466.pdf)

FastCorrelativeScanMatcher는 다양한 그리드 해상도에서 작동하고 잘못된 scan matching을 효율적으로 제거하기 위해 "Branch and bound" 기술을 사용함
(깊이를 조절할 수 있는 exploration tree에서 작동함)

    - 저해상도의 Map에서 시작해서 pose를 추론해 점점 고해상도 내의 pose를 찾아냄
    - Node가 pose의 후보, Height는 Higher resolution을 나타냄

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/7.1.png)

    - POSE_GRAPH.constraint_builder.fast_correlative_scan_matcher.branch_and_bound_depth
    - POSE_GRAPH.constraint_builder.fast_correlative_scan_matcher_3d.branch_and_bound_depth
    - POSE_GRAPH.constraint_builder.fast_correlative_scan_matcher_3d.full_resolution_depth

FastCorrelativeScanMatcher에 최소 점수 이상의 matching 이 있으면 Ceres Scan Matcher 에 제공되어 pose를 개선함

    - POSE_GRAPH.constraint_builder.min_score
    - POSE_GRAPH.constraint_builder.ceres_scan_matcher_3d
    - POSE_GRAPH.constraint_builder.ceres_scan_matcher

Ceres 는 다양한 잔차(residuals)에 따라 submap을 재 정렬하는데 사용됨

잔차는 weighted cost function을 사용해 계산됨

Global 최적화에 많은 데이터 소스를 고려하는 cost function들이 있음(global(loop closure)제약, non-global(matcher)제약, IPU 가속, 회전 측정, local SLAM의 대략적인 pose 측정, odometry 등)

가중치와 Ceres 의 옵션들은 아래와 같이 구성할 수 있음

    - POSE_GRAPH.constraint_builder.loop_closure_translation_weight
    - POSE_GRAPH.constraint_builder.loop_closure_rotation_weight
    - POSE_GRAPH.matcher_translation_weight
    - POSE_GRAPH.matcher_rotation_weight
    - POSE_GRAPH.optimization_problem.*_weight
    - POSE_GRAPH.optimization_problem.ceres_solver_options

IMU 잔류의 일부에서 최적화 문제는 IMU pose에 유연성을 제공함

Ceres는 IMU와 tracking 프레임 사이에 외적인 눈금을 최적화하는데 자유로움

IMU pose를 신뢰하지 않는다면 Ceres의 global 최적화 결과가 로깅되며 외적인 눈금을 개선하는데 사용됨

만일 Ceres가 IMU pose를 최적화 하지 않는다면, 외적인 눈금을 신뢰한다는 것이고 해당 pose를 일정하게 만들 수 있음

    - POSE_GRAPH.optimization_problem.log_solver_summary
    - POSE_GRAPH.optimization_problem.use_online_imu_extrinsics_in_3d

잔류에서 outliers의 영향은 Huber loss 함수에 의해 다루어 지며 Huber scale 값으로 구성됨

Huber sale이 클스록 outliers에 더 많이 영향을 줌

    - POSE_GRAPH.optimization_problem.huber_scale

궤적이 완료되면 Cartographer는 새로운 global optimization을 실행하고 이전 global optimization 보더 더 많은 iterations를 수행함

이 작업 Cartographer의 최종적인 결과를 다듬기 위해 수행되며 일반적으로 실시간일 필요가 없어 많은 반복 횟수를 선택하는 것이 좋음

    - POSE_GRAPH.max_num_final_iterations
