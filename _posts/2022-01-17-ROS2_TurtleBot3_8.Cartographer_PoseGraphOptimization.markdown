---
layout: post
title:  "ROS2_TurtleBot3_8.&nbsp;Cartographer_PoseGraphOptimization"
date:   2022-01-18 05:18:15 +0700
categories: [ROS2]
use_math: true
---

Ubuntu 20,04 기반 ROS2 Foxy 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> Cartographer PoseGraphOptimization

#### Pose Graph Optimization

로봇이 라이더로 처음 측정하면 이를 Pose 그래프(오른쪽)로 옮김

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.1.png)

로봇이 조금 움직이면 실제 Pose와 Noise로 인한 오차로 Estimated 로봇 Pose가 있음

Estimated 로봇 Pose를 Pose Graph로 다시 옮김

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.2.png)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.3.png)

맵에서 어딘지는 몰라도 두 Pose간에 얼마만큼 떨어졌는지는 알 수 있음

두 Pose 간에 Edge 로 이어줌 두번째 측정된 포즈의 위치에 따라 라이더로 관측된 것을 조정할 수 있음

(앞 뒤나 회전 시 관측 값들이 이동가능)

계속 로봇이 가면 다시 측정된 것을 Pose Graph로 옮기고 Edge로 이어줌

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.6.png)

이제 3개의 노드와 2개의 edge로 구성된 그래프가 완성됨

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.7.png)

이후 로봇이 한 바퀴 돌았다고 가정하면 아래와 같은 Pose Graph가 생성됨

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.8.png)

여기서 마지막 Pose(Current Pose)와 처음 Pose 를 보면 같은 Feature의 환경 값을 가지고 있음

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.9.png)

이 두 Pose를 연결하면 Close Loop를 만듬

여기서 Loop Closure가 생기며 해당 Edge(파란색)는 Tension을 가지고 있음

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.10.png)

두 Pose에서 측정된 Feature 들이 얼마나 가까운지 확인 하며 그림에서는 거의 똑같기 때문에 두 Pose 간 당겨서 일치 시키고 싶어함(파란 Edge 부분)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.11.png)

해당 Pose 들이 당겨지면서 최적화하는 과정이 진행되게 됨(현재까지 진행된 전체 포즈들의 에러가 나이스하게 줄어듬)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.12.png)

왼쪽 위의 선들이 연결되어 있지 않고 아직 부정확하지만 로봇이 움직이면서 점점 최적화 과정을 거쳐 맵을 만들게 됨

다시 아래와 같은 Loop Closure가 발생했다고 하면 점차 당겨 에러를 최소화 함

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.13.png)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.14.png)

---

이렇게 만든 Pose Graph를 맵으로 어떻게 만들 것 인가?

간단하게 Binary Occupancy Grid(격자맵)

Grid 셀에 집어 넣어 장애물이 있는 것은 1 아닌 것은 0로 채우는 과정

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.15.png)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/8.16.png)

중간중간 hole이 있지만 전체적으로 보기 좋음

Probabilistic Occupancy Grid는 중간 중간 hole을 확률적으로 채워 더 확률적으로 정확한 맵을 생성할 수 있음

---

