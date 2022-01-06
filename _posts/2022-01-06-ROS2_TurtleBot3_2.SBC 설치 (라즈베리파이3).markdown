---
layout: post
title:  "ROS2_TurtleBot3_2.&nbsp;SBC 설치 (라즈베리파이3)"
date:   2022-01-06 05:18:15 +0700
categories: [ROS2]
---

Ubuntu 18,04 기반 ROS2 Dashing Diademeta 로 TurtleBot3 Waffle Pi 구동 과정 정리

TurtleBot3 Waffle Pi 패키지에 라즈베리파이3가 들어있어 라즈베리파이 3 기준으로 SBC 설치

---

> SBC Setup

#### Rasberry Pi 3B+ 이미지 설치

[SBC_OS이미지다운로드](http://old-releases.ubuntu.com/releases/18.04.4/)

- 위의 경로에서 ubuntu-18.04.4-preinstalled-server-arm64+raspi3.img.xz 다운
- 아래 명령어로 압축 해제하여 img 파일 추출

``` bash
sudo apt-get install xz-utils
unxz ubuntu-18.04.4-preinstalled-server-arm64+raspi3.img.xz
```

Raspberry Pi Imager 를 통해 이미지 write 후 구동

---

#### Raspberry Pi 3B+ 설정

- Raspberry Pi 3B+ 설정은 아래 링크 3.2.6, 3.2.7 수행
- 와이파이 설정 및 ssh 설정
- swap space 추가

[RaspberryPi3B+_설정]https://emanual.robotis.com/docs/en/platform/turtlebot3/sbc_setup/#sbc-setup


--- 
#### Raspberry Pi 3B+ ROS 2 Dashing Diademata 설치

- locale 설정

``` bash
sudo apt update && sudo apt install locales
sudo locale-gen en_US en_US.UTF-8
sudo update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
export LANG=en_US.UTF-8
```

- Setup sources

``` bash
sudo apt update && sudo apt install curl gnupg2 lsb-release
sudo curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/ros2.list > /dev/null
```

- ROS2 패키지 설치

``` bash
sudo apt update
sudo apt install ros-dashing-ros-base
```

- ROS 패키지 설치 및 빌드

``` bash
sudo apt install python3-argcomplete python3-colcon-common-extensions libboost-system-dev build-essential
sudo apt install ros-dashing-hls-lfcd-lds-driver
sudo apt install ros-dashing-turtlebot3-msgs
sudo apt install ros-dashing-dynamixel-sdk
mkdir -p ~/turtlebot3_ws/src && cd ~/turtlebot3_ws/src
git clone -b dashing-devel https://github.com/ROBOTIS-GIT/turtlebot3.git
cd ~/turtlebot3_ws/src/turtlebot3
rm -r turtlebot3_cartographer turtlebot3_navigation2
cd ~/turtlebot3_ws/
echo 'source /opt/ros/dashing/setup.bash' >> ~/.bashrc
source ~/.bashrc
colcon build --symlink-install --parallel-workers 1
echo 'source ~/turtlebot3_ws/install/setup.bash' >> ~/.bashrc
source ~/.bashrc
```

- OpenCR USB 포트 셋팅

``` bash
cd ~/turtlebot3_ws/src/turtlebot3/turtlebot3_bringup
sudo cp 99-turtlebot3-cdc.rules /etc/udev/rules.d/
turtlebot3_bringup/script/99-turtlebot3-cdc.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules
sudo udevadm trigger
```

- ROS Domain ID 셋팅

``` bash
echo 'export ROS_DOMAIN_ID=30 #TURTLEBOT3' >> ~/.bashrc
source ~/.bashrc
```
---
