---
layout: post
title:  "ROS2_TurtleBot3_1.&nbsp;RemotePC ROS2 Foxy Setup"
date:   2022-01-06 04:18:15 +0700
categories: [ROS2]
---

Ubuntu 20,04 기반 ROS2 Foxy 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> Remote PC Setup

#### ROS2 Foxy 설치


``` bash
wget https://raw.githubusercontent.com/ROBOTIS-GIT/robotis_tools/master/install_ros2_foxy.sh
sudo chmod 755 ./install_ros2_foxy.sh
bash ./install_ros2_foxy.sh
```

- 실제로는 많은 설치가 있지만 위에 스크립트 파일로 쉽게 설치 가능
- 설치 실패 시 아래 링크를 통해 진행

[ROS2 Foxy 설치 공식 문서](https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html)

---

#### ROS2 의존 패키지 설치

- Gazebo11 설치

``` bash
sudo apt-get install ros-foxy-gazebo-*
```

- Cartographer 설치 (2D,3D 멀티플랫폼 SLAM 지원 패키지)

``` bash
sudo apt install ros-foxy-cartographer
sudo apt install ros-foxy-cartographer-ros
```

- Navigation2 설치

``` bash
sudo apt install ros-foxy-navigation2
sudo apt install ros-foxy-nav2-bringup
```

- TurtleBot3 패키지 설치

``` bash
source ~/.bashrc
sudo apt install ros-foxy-dynamixel-sdk
sudo apt install ros-foxy-turtlebot3-msgs
sudo apt install ros-foxy-turtlebot3
```

- ROS 환경 설정

``` bash
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
source ~/.bashrc

```

마지막 source 명령어 실행 시 

bash: /home/{$YOUR_ACCOUNT}/turtlebot3_ws/install/setup.bash: No such file or directory

위의 에러는 무시해도 됨(apt install로 TurtleBot3 설치 시 warning 발생)

---
