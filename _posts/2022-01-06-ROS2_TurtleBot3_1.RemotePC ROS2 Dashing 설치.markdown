---
layout: post
title:  "ROS2_TurtleBot3_1.&nbsp;RemotePC ROS2 Dashing Setup"
date:   2022-01-06 04:18:15 +0700
categories: [ROS2]
---

Ubuntu 18,04 기반 ROS2 Dashing Diademeta 로 TurtleBot3 Waffle Pi 구동 과정 정리

최신버전은 ROS2 Foxy 이지만 Remote PC 가 Ubuntu 18.04 이며, 기본 Package 들도 Dashing 이 많아 ROS2 Dashing 으로 선정하여 진행함

---

> Remote PC Setup

#### ROS2 Dashing 설치


``` bash
sudo apt-get update
wget https://raw.githubusercontent.com/ROBOTIS-GIT/robotis_tools/master/install_ros2_dashing.sh
chmod 755 ./install_ros2_dashing.sh
bash ./install_ros2_dashing.sh
```

- 실제로는 많은 설치가 있지만 위에 스크립트 파일로 쉽게 설치 가능
- 설치 실패 시 아래 링크를 통해 진행

[ROS2 Dasing 설치 공식 문서](https://docs.ros.org/en/dashing/Installation/Ubuntu-Install-Binary.html)

---

#### ROS2 의존 패키지 설치

- Colcon 설치

``` bash
sudo apt install python3-colcon-common-extensions
```

- Gazebo9 설치 (ROS2 Foxy는 Gazebo11 이지만 Dashing은 Gazebo9)

``` bash
curl -sSL http://get.gazebosim.org | sh
```

- Cartographer 설치 (2D,3D 멀티플랫폼 SLAM 지원 패키지)

``` bash
sudo apt install ros-dashing-cartographer
sudo apt install ros-dashing-cartographer-ros
```

- Navigation2 설치

``` bash
sudo apt install ros-dashing-navigation2
sudo apt install ros-dashing-nav2-bringup
```

- vsctool 설치 (프로젝트 워크스페이스 유지보수 - version-control system)

``` bash
sudo apt install python3-vcstool
```

---
