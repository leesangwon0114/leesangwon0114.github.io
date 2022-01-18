---
layout: post
title:  "ROS2_TurtleBot3_9.&nbsp;SLAM구동"
date:   2022-01-18 06:18:15 +0700
categories: [ROS2]
use_math: true
---

Ubuntu 20,04 기반 ROS2 Foxy 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> SLAM 구동

#### Run SLAM Node

ssh 로 접속하여 SBC에서 turtlebot3_bringup robot.launch 실행

``` bash
ssh ubuntu@{IP_ADDRESS_OF_RASPBERRY_PI}
ssh ubuntu@192.168.1.177
export TURTLEBOT3_MODEL=waffle_pi
ros2 launch turtlebot3_bringup robot.launch.py
```

Remote PC에서 SLAM 노드 실행

``` bash
export TURTLEBOT3_MODEL=waffle_pi
ros2 launch turtlebot3_cartographer cartographer.launch.py
```

``` bash
export TURTLEBOT3_MODEL=waffle_pi
ros2 run turtlebot3_teleop teleop_keyboard
```