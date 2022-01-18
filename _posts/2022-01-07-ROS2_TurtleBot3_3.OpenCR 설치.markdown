---
layout: post
title:  "ROS2_TurtleBot3_3.&nbsp;OpenCR 설치"
date:   2022-01-07 05:18:15 +0700
categories: [ROS2]
---

Ubuntu 20,04 기반 ROS2 Foxy 로 TurtleBot3 Waffle Pi 구동 과정 정리

---

> OpenCR Setup

#### OpenCR firmware 업로드

- OpenCR을 라즈베리파이3에 micro USB케이블로 연결
- dmesg | tail 명령어를 통해 ttyACM0에 정상 연결 확인
- OpenCR firmware를 업로드 하기 위한 라즈베라피이 패키지 설치
- sudo apt-get update 시 Release file is not valid yet Error 가 발생하여 curl로 새로운 키 발급 추가함(이후 update 진행)

``` bash
sudo dpkg --add-architecture armhf
curl http://repo.ros2.org/repos.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install libc6:armhf
```

- OpenCR 환경 설정

``` bash
export OPENCR_PORT=/dev/ttyACM0
export OPENCR_MODEL=waffle
rm -rf ./opencr_update.tar.bz2
```

- Firmware 와 Loader 다운로드 및 압축 해제

``` bash
wget https://github.com/ROBOTIS-GIT/OpenCR-Binaries/raw/master/turtlebot3/ROS2/latest/opencr_update.tar.bz2
tar -xjf ./opencr_update.tar.bz2
```

- OpenCR로 Firmware 업로드

``` bash
cd ~/opencr_update
./update.sh $OPENCR_PORT $OPENCR_MODEL.opencr
```

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/3.1.png)

위와 같은 화면이면 업로드 성공(노래 소리가 나옴)

여기까지 설정 후 HW 조립

![Alt text](http://leesangwon0114.github.io/static/img/ROS2/3.2.jpg)

---

#### Bringup TurtleBot3 
- TurtleBot3 APP 시작을 위한 Basic Packages 가져오기
- ssh 를 통해 쉘 접속 후 아래 명령어 실행

``` bash
export TURTLEBOT3_MODEL=waffle_pi
ros2 launch turtlebot3_bringup robot.launch.py
```

- remote PC에서 키보드를 통한 제어 실행(Teleoperation)

``` bash
export TURTLEBOT3_MODEL=waffle_pi
ros2 run turtlebot3_teleop teleop_keyboard
```
RemotePC의 키보드를 통한 원격 제어 해보기

w/x 는 속도 증감

a/d는 각도 증가 감소

s 는 멈춤
