---
layout: post
title:  "ROS2_TurtleBot3_5.&nbsp;Cartographer"
date:   2022-01-13 05:18:15 +0700
categories: [ROS2]
use_math: true
---

Ubuntu 18,04 기반 ROS2 Dashing Diademeta 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> Cartographer(2D/3D Lidar Graph SLAM 기반 알고리즘)

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

- Cartographer 은 크게 Local SLAM 과 Gloal SLAM으로 나뉨

> Cartoprapher Paper

https://research.google/pubs/pub45466/

---

#### Cartographer 구조

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.3.png) ![Alt text](http://leesangwon0114.github.io/static/img/ROS2/5.5.png)

- 