---
layout: post
title:  "ROS2_TurtleBot3_2.&nbsp;SBC 설치 (라즈베리파이4)"
date:   2022-01-06 05:18:15 +0700
categories: [ROS2]
---

Ubuntu 20,04 기반 ROS2 Foxy 로 TurtleBot3 Waffle Pi 구동 과정 정리


TurtleBot3 Waffle Pi 패키지에 라즈베리파이3가 들어있으나 라즈베리파이 4 4GB 기준으로 SBC 설치

---

> SBC Setup

#### Rasberry Pi 4 이미지 설치

[SBC_OS이미지다운로드](https://www.robotis.com/service/download.php?no=2064)

- 압축 해제하여 img 파일 추출

Raspberry Pi Imager 를 통해 이미지 write 후 구동

---

#### Raspberry Pi 4 설정

``` bash
sudo apt-get install gparted
```

- gparted 앱 실행 후 sd카드로 선택
- 노란색 부분의 오른쪽 클릭 후 Resize/Move 옵션 선택
- 팝업창이 뜨면 오른쪽 선에서 제일 오른쪽으로 드래그해서 늘려줌
- 이후 Resize/Move 버튼 클릭
- 초록색 체크 버튼을 통해 적용

SD 카드 resize 후 wifi 설정

``` bash
cd /media/$USER/writable/etc/netplan
sudo nano 50-cloud-init.yaml
```

WIFI_SSID 와 WIFI_PASSWORD 를 지우고 각각의 값 입력


[RaspberryPi4_설정]https://emanual.robotis.com/docs/en/platform/turtlebot3/sbc_setup/#sbc-setup


--- 
#### Raspberry Pi 4 ROS 2 Foxy 설정

- 라즈베리파이 4 HDMI 케이블 연결 후 부팅
- 초기 비번은 ubuntu / turtlebot 임
- default ROS_DOMAIN_ID도 30으로 되어 있음(Remote PC와 이 값이 같아야함)
