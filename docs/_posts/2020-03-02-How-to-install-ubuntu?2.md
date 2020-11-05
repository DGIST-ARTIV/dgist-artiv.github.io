---
date: 2020-03-02
layout: post
title: Setting the environment for development with deeplearning[part 2]
subtitle: 딥러닝을 이용한 개발을 위한 환경 설정 하기[part 2]
description: 우분투, 쿠다, cudnn, pytorch, tensorflow, opencv 등 환경 세팅[part 2]
image: https://user-images.githubusercontent.com/53460541/94029160-7a5ebd00-fdf7-11ea-814c-e7d313a7b52b.png
optimized_image: https://user-images.githubusercontent.com/53460541/94029160-7a5ebd00-fdf7-11ea-814c-e7d313a7b52b.png
category: vision
tags:
  - vision
author: eunbin
---

# 딥러닝을 이용한 개발을 위한 환경 설정[part 2]

### part 1에 이어서...
저번 시간까지 graphic card에 맞는 드라이버, 쿠다까지 설치했다.  
이번 시간엔 cuda와 맞는 cudnn과 tensorflow, keras, pytorch, opencv 설치를 소개하고자 한다.

### 환경 설정 3단계: cuda에 맞는 cudnn 설치하기
2단계에서 설치한 cuda 버전에 맞는 cudnn을 설치해야한다. 우리는 cuda 10.0을 설치했으므로 그에 맞는 cudnn을 다운 받을 수 있다.
https://developer.nvidia.com/rdp/cudnn-download 이 링크를 타고 들어가 회원가입을 한 후에 cudnn을 다운받을 수 있다.  
cudnn은 다운로드한 파일을 압축을 풀어서 경로에 맞춰 복사해서 붙여넣어 주면된다.  

~~~(bash)
cd ~
tar xvzf cudnn-10.0-linux-x64-v7.6.5.32.tgz (여기는 다운 받은 파일에 따라 다르다)
sudo cp cuda/include/cudnn.h /usr/local/cuda-10.0/include/
sudo cp cuda/lib64/* /usr/local/cuda-10.0/lib64/
~~~

### 환경 설정 4단계: framework 설치하기
framework를 사용하는 것이 훨씬 편하게 네트워크를 구성할 수 있다.  

**환경 설정은 앞에서 봐왔듯이 버전을 맞춰서 설치하는 것이 중요하다.**  

1. tensorflow
runfile은 Nvidia development 사이트에서 다운 받을 수 있다.  
~~~(bash)
pip3 install tensorflow-gpu==(version)
~~~
우리는 version 1.14.0을 설치했다.  

2. keras
keras는 tensorflow를 backend로 이용하기 때문에 tensorflow 버전에 맞는 keras 설치해야한다.  
~~~(bash)
pip3 install keras==(version)
~~~
우리는 version 2.3.1을 설치했다.

3. pytorch
https://pytorch.org/get-started/previous-versions/ 이 링크는 pytorch 공식 사이트이다.  
version에 맞게 참고하여 설치하면 된다.  
~~~(bash)
pip3 install torch==1.2.0 torchvision==0.4.0
~~~
우리는 version 1.2.0을 설치했다.  

framework가 잘 설치된지 확인하려면, python3 에서 import 한 후 버전 확인을 하면 된다. 
~~~(bash)
# 예시
python3
import tensorflow as tf
tf.__version__
~~~

### 환경 설정 5단계: opencv 설치하기
camera를 이용해 vision 처리를 해야하는 연구를 하고 있다면 opencv는 필수적이고, 잘 못 설치하게 되면 돌이킬 수 없는... 크흠 아무튼 중요한 단계이다.  

opencv 버전에 따라 사용할 수 있는 기능이 다를 수 있으므로 check 필요!  
이 링크를 따라 이용했다.  
여기서 주의할 점!  
**이전 버전이 있다면 autoremove를 하라고 되어있는데, 이걸 함부로 할 시에 이전에 깔았던 모든 것이 사라질 수 있다.. autoremove 이용방법을 알고 사용하길..**  
~~이것때문에 ros1, ros2 싹 날린적이 한 두번이 아니라는 점...~~  
https://webnautes.tistory.com/1186

