---
layout: post
title:  "MLStudy&nbsp;Install Tensorflow-gpu in Ubuntu"
date:   2018-05-24 07:28:15 +0700
categories: [MLStudy]
---

#### 우분투 설치 시 유의사항

CUDA 설치 시 GUI가 없는 환경에서 작업 시 한글 깨짐 현상이 발생하므로 우분투 영문판 설치를 권장(아닐 경우 download 받은 파일을 home에다 복사 한 후 진행)

##### 우분투 한글 설정

한글설치

- sudo apt-get install fcitx-hangul
- System Settings > Language Support를 설치 후 Keboard input method system을 fcitx로 변경
- 재부팅

한영키설정

- System Settings > Text Entry 에서 Hangul(Fcitx)를 + 버튼을 통해 추가
- AllSettings > Keyboard > Shortcuts Tab > Typing을 선택
- Switch to Next source, Switch to Previous sourc, Compose Key, Alternative Characters Key를 모두 backspace를 눌러 Disabled로 선택함
- Compose Key의 Disabled를 길게 눌러 Right Alt를 선택(아래꺼 하기전에 먼저해야함)
- Switch to next source는 한영키를 눌러 Multikey를 선택
 
---

이제 본격적으로 tensorflow gpu 설치

CUDA설치에는 이광식님 블로그 활용함 

[http://www.kwangsiklee.com/ko/2017/07/%EC%9A%B0%EB%B6%84%ED%88%AC-16-04%EC%97%90%EC%84%9C-cuda-%EC%84%B1%EA%B3%B5%EC%A0%81%EC%9C%BC%EB%A1%9C-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0/#comment-41
](http://www.kwangsiklee.com/ko/2017/07/%EC%9A%B0%EB%B6%84%ED%88%AC-16-04%EC%97%90%EC%84%9C-cuda-%EC%84%B1%EA%B3%B5%EC%A0%81%EC%9C%BC%EB%A1%9C-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0/#comment-41 "이광식님 블로그")

#### CUDA 9.0 다운로드

유의사항 : 반드시 tensorflow에서 추천하는 버전(테스트된 버전)을 설치해야함

[https://developer.nvidia.com/cuda-toolkit-archive](https://developer.nvidia.com/cuda-toolkit-archive "CUDA legacy")

tensorflow-gpu 1.8 버전을 설치를 위해 위의 CUDA legacy 사이트에서 CUDA 9.0 run 파일 다운로드

![Alt text](http://leesangwon0114.github.io/static/img/MLStudy/1.1.PNG)

---

#### Nouveau 드라이버 제거

> sudo apt-get remove nvidia* && sudo apt autoremove

> sudo apt-get install dkms build-essential linux-headers-generic

/etc/modprobe.d/blacklist.conf 파일을 열어 

> sudo nano /etc/modprobe.d/blacklist.conf  

아래의 내용을 추가

>blacklist nouveau
>
>blacklist lbm-nouveau
>
>options nouveau modeset=0
>
>alias nouveau off
>
>alias lbm-nouveau off


![Alt text](http://leesangwon0114.github.io/static/img/MLStudy/1.2.jpg)


보험으로 아래 명령어도 실행

> echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf

커널을 재빌드

> sudo update-initramfs -u

우분투 재부팅 

> sudo reboot

---

#### Nvidia 그래픽 드라이버 설치

아래 Nvidia 사이트에서 본인에게 맞는 드라이버 설치

[http://www.nvidia.com/Download/index.aspx?lang=en-us](http://www.nvidia.com/Download/index.aspx?lang=en-us "Nvidia driver")

여기서 부터 GUI 종료 시켜야지 error 가 발생하지 않음

바로 실행하면 아래와 같은 error 발생

![Alt text](http://leesangwon0114.github.io/static/img/MLStudy/1.3.jpg)

재부팅 후 Ctrl + Alt + F1을 눌러 콘솔창으로 변경 후 로그인(GUI로 돌아가고 싶으면 Ctrl + Alt + F7)

GUI 관련 서비스 종료

> sudo service lightdm stop

> 아까 다운받은 폴더로 이동

> sudo sh ./NVIDIA-Linux-x86_64-390.59.run

위의 run 파일은 본인이 받은 파일명 기입

![Alt text](http://leesangwon0114.github.io/static/img/MLStudy/1.4.jpg)

물어보는 질문들 모두 default 로 enter 함

---

#### CUDA 9.0 설치

아까 다운받은 CUDA 설치하는 과정임

Nvidia 설치 완료된 상태 그대로 실행해야함(콘솔창 상태)

>> sudo sh ./cuda_9.0.176_384.81_linux.run

More % 창이 뜨면 enter를 눌려 100% 까지 간 후 EULA창에서 accept 입력

cuda run 설정에서 개인적으로 아래와 같이함

![Alt text](http://leesangwon0114.github.io/static/img/MLStudy/1.5.jpg)

Summary에서 Driver Installed 를 보면 성공

> sudo reboot

**재부팅 후 GUI 환경에서 bashrc에 아래 2줄 추가**

> sudo nano ~/.bashrc
> 
> 위의 command로 bashrc를 연 후
> 
> export PATH=$PATH:/usr/local/cuda-9.0
>
> export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-9.0/lib64

---

#### 우분투 커널 업데이트 방지

System Settings -> Software & updates에서 Updates 탭에서

Important security updates(xenial-security) 만 남겨둠

![Alt text](http://leesangwon0114.github.io/static/img/MLStudy/1.6.jpg)

---

#### cuDNN 7.0 설치

cuDNN Archive 사이트로 이동

[https://developer.nvidia.com/rdp/cudnn-archive](https://developer.nvidia.com/rdp/cudnn-archive "cuDNN Archive")

Download cuDNN v7.0.5 (Dec 5, 2017), for CUDA 9.0 를 선택 후

cuDNN v7.0.5 Library for linux 클릭

cudnn-9.0-linux-x64-v7.tgz 가 다운로드 될 것임

다운된 폴더로 가서

> tar -xzvf cudnn-9.0-linux-x64-v7.tgz
>
> sudo cp cuda/include/cudnn.h /usr/local/cuda/include
> 
> sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
> 
> sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

이후 CUDA_HOME 설정

> export CUDA_HOME=/usr/local/cuda

---

#### Tensorflow gpu 설치

Tensorflow 사이트에서 Virtualenv 방식을 권장하므로 해당 방식으로 설치

python 3 기반으로 설치

[https://www.tensorflow.org/install/install_linux](https://www.tensorflow.org/install/install_linux "Installing with Virtualenv")

> sudo apt-get install python3-pip python3-dev python-virtualenv
>
> virtualenv --system-site-packages -p python3 ~/tensorflow
> 
> source ~/tensorflow/bin/activate
> 
> (tensorflow)$ easy_install -U pip
> 
> pip3 install --upgrade tensorflow-gpu

---

#### Run a short TensorFlow Program

![Alt text](http://leesangwon0114.github.io/static/img/MLStudy/1.7.jpg)