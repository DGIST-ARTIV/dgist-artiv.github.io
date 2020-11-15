---
date: 2020-03-02
layout: post
title: Setting the environment for development with deeplearning[part 1]
subtitle: 딥러닝을 이용한 개발을 위한 환경 설정 하기[part 1]
description: 우분투, 쿠다, cudnn, pytorch, tensorflow, opencv 등 환경 세팅[part 1]
image: https://user-images.githubusercontent.com/53460541/94029160-7a5ebd00-fdf7-11ea-814c-e7d313a7b52b.png
optimized_image: https://user-images.githubusercontent.com/53460541/94029160-7a5ebd00-fdf7-11ea-814c-e7d313a7b52b.png
category: vision
tags:
  - vision
author: eunbin
---

# 딥러닝을 이용한 개발을 위한 환경 설정[part 1]

### 우최몇?ㅎㅎ
개발자라면 환경 설정 단계는 무조건 거쳐 가야한다. (환경설정이 제일 어려워...ㅎ)   
우리들 사이에는 우최몇?(우분투 최대 몇번 까지 깔아봤어?)이라는 말을 유행어처럼 쓸 정도로 우분투를 깔고 지우고를 반복했다.   
나는 하루에 15번은 넘게 깔아본 것 같다..ㅎ   
우분투를 새롭게 설치하고 cuda, cudnn, pytorch, tensorflow, opencv 등을 한 번에 깔끔하게 설치하는 것을 추천한다.   
만약, 설치가 잘못되었다! 버전을 새롭게 깔아야한다 할 때는 조심스럽게 지워야 한다. 아니면 우분투 자체를 다시 깔게 될수도....

### 우분투 설치
우분투 설치 뭐가 어렵겠어? 그냥 설치하면 되지! 라고 생각할지 모르지만 잘못하다간 파티션을 잘 못 나눠 window가 날아갈 수 있고
고이 모셔놨던 데이터셋이 다 날아갈 수 있다. 우분투가 담긴 usb를 가지고 잘 설치해야한다.
#### 우분투 설치하면서 겪었던 상황들
1. grub이 깨진다.. 이건 3일만에 해결했다.. 검은 화면에 파티션이 잘 나뉘었나 확인하는 명령어 밖에 칠 수 없는 암울한 상황이 있을 수 있다. 
다시 우분투를 설치해도 계속되는 상황에 as까지 맡길 생각을 했지만 다행히 grub recovery를 통해 살릴 수 있었다.
2. 부팅은 다 된 것 같은데 화면이 안켜져요...ㅠ 이런 경우는 그래픽 드라이버를 설치하기 전에는 계속 화면이 안뜰거에요.

### 환경 설정 1단계: graphic card에 맞는 driver 설치하기
0. 내가 가지고 있는 graphic card가 뭐지? 확인하기
Titan X, Titan Xp, Gtx 1080ti 등등...
1. repository 추가
~~~(bash)
sudo add-apt-repository ppa:graphics-drivers/ppa
sudo apt update
~~~
2. 설치가 가능한 드라이버 목록 확인
~~~(bash)
apt-cache search nvidia
~~~
엄청 많은 목록이 나올 것이다. 가장 높은 버전의 driver를 설치하면 된다.
3. apt로 드라이버 설치
~~~(bash)
sudo apt-get install nvidia-driver-(버전)
# 예시: sudo apt-get install nvidia-driver-440
~~~
4. 설치가 다 되면 reboot하기!

**우분투 설치하고 이 과정까지 하면 화면이 안뜨는 상황이 해결될 것이다.**

### 환경 설정 2단계: Cuda 설치하기
1. 그래픽 드라이버에 맞는 쿠다를 설치해야한다. 나는 runfile을 통해 쿠다를 설치했다. 
runfile은 Nvidia development 사이트에서 다운 받을 수 있다. 
2. 설치하기   
우리는 쿠다 10.0을 이용했다.
~~~(bash)
sudo sh cuda_10.0.130.410.48_linux.run
~~~
하고 q를 쳐주면 다음과 같은 질문들을 한다. 대답은 다음과 같이 해준다.   
> Do you accept the previously read EULA? accept/decline/quit: **accept** 
Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 430.40? (y)es/(n)o/(q)uit: **n**   
Install the CUDA 10.0 Toolkit? (y)es/(n)o/(q)uit: **y**   
Enter Toolkit Location [ default is /usr/local/cuda-8.0 ]: **Enter 치기**   
Do you want to install a symbolic link at /usr/local/cuda? (y)es/(n)o/(q)uit: **y**   
Install the CUDA 10.0 Samples? (y)es/(n)o/(q)uit: **y**   
Enter CUDA Samples Location [ default is /home/python-kim ]: **Enter 치기**   

쿠다 10.0 패치도 설치해준다.   
~~~(bash)
sudo sh cuda_10.0.130.1_linux.run
~~~
다음과 같은 화면이 뜰 것이다. 다음과 같은 대답을 해준다.    
> Do you accept the previously read EULA? accept/decline/quit: **accept**   
Enter CUDA Toolkit installation directory [ default is /usr/local/cuda-10.0 ]: **Enter 치기**   
Installation complete!
Installation directory: /usr/local/cuda-10.0

3. 경로 지정해주기
~~~(bash)
sudo nano ~/.bashrc
~~~
alt+/를 통해 맨 마지막 줄로 내려가 다음 세 줄을 입력해준다.
~~~(bash)
export PATH=$PATH:/usr/local/cuda-10.0/bin
export CUDADIR=/usr/local/cuda-10.0
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-10.0/lib64
~~~
4. bashrc 소스해주기   
~~~(bash)
source ~/.bashrc
~~~

드디어 쿠다 설치가 끝났다!!!   
생각보다 블로그 글이 길어져서 cudnn과 tensorflow, pytorch, opencv 설치는 2탄에 계속..

