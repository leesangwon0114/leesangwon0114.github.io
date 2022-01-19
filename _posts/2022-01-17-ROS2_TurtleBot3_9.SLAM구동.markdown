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
# ssh ubuntu@192.168.1.178
export TURTLEBOT3_MODEL=waffle_pi
ros2 launch turtlebot3_bringup robot.launch.py
```

Remote PC에서 SLAM 노드 실행

``` bash
export TURTLEBOT3_MODEL=waffle_pi
ros2 launch turtlebot3_cartographer cartographer.launch.py
```

처음에 값이 안받아질 수 있는데 센서 값이 잘오는지 확인해봐야함

``` bash
ros2 topic list -t
ros2 topic echo /scan
```

/scan 토픽에 대해 값이 잘 오는지 확인해보기

원격조정으로 맵 그린 후

``` bash
export TURTLEBOT3_MODEL=waffle_pi
ros2 run turtlebot3_teleop teleop_keyboard
```

맵 저장

``` bash
ros2 run nav2_map_server map_saver_cli -f ~/map
```

맵 파일(default로 /home/${username} 에 map.pgm, map.yaml 파일이 저장됨)

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/9.1.png)

맵 지도 결과

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/9.2.png)

---