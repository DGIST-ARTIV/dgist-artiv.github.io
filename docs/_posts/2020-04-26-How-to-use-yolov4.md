---
date: 2020-04-26
layout: post
title: How to use yolov4
subtitle: yolov4 실행시켜보기
description: yolov4 실행시켜보기
image: https://user-images.githubusercontent.com/59161083/91840097-19761600-ec8b-11ea-978f-94a8d6633d56.png
category: vision
tags:
  - autonomous
  - vision
  - object detection
  - yolov3
  - yolov4
author: gu
---


# how to use yolo_v4
Author: 이  구
> reference: https://github.com/AlexeyAB/darknet

### 2020/04/26 
#### darknet 빌드

1. 다운
~~~(bash)
mkdir yolo_v4
cd yolo_v4
git clone https://github.com/AlexeyAB/darknet.git
~~~

2. 빌드
~~~(bash)
cd darknet/
mkdir build-release
cd build-release/
cmake ..
make
make install
~~~

#### yolo_v4
1. from webcam
~~~(bash)
./darknet detector demo ./cfg/coco.data ./cfg/yolov4.cfg ./yolov4.weights -c 0
~~~

2. from video
~~~(bash)
./darknet detector demo cfg/coco.data cfg/yolov4.cfg yolov4.weights -ext_output test.mp4
~~~

yolov4.weights파일은 [링크](https://drive.google.com/uc?id=1cewMfusmPjYWbrnuJRuKhPMwRe_b9PaT&export=download)에서 다운받을 수 있다.

이용하는 사람이 너무 많아서 아직 weights 파일을 다운받을 수 없었다.   
![img](https://user-images.githubusercontent.com/59161083/91834463-0b23fc00-ec83-11ea-8566-2cc41cbedb71.png)   
직접 실행해본 결과, 4~6 fps 정도 나온다.   

### 2020/04/30
4~6fps. 작년에는 적어도 30fps 정도 나왔다고 한다. 느린 이유를 생각해보다가, 그래픽 드라이버를 다시 깔아보기로 했다.   
그래픽 드라이버를 다시 설치하던 중, 우분투가 죽었다. E5-219호의 컴퓨터를 모두 밀었다.   

### 2020/05/01
그래픽 드라이버, CUDA, cudnn, opencv 설치

### 2020/05/02 1:59AM
makefile의 CUDA,cudnn, opencv를 똑바로 셋팅하고 build 한 후, 실행한 결과   
1. 싸구려 웹캠 -> 약 30fps   
2. 비싼 웹캠 -> 약 5fps   
3. 직접 찍은 도로주행 영상(4_28_18_43.avi) -> yolo_v3: 약 43fps / yolo_v4: 약 35fps   

#### 성능평가 (4_28_18_43.avi 파일 사용)   
yolo inference는 gpu 하나만 사용한다(titan xp사용). train할 때는 gpu 여러개 사용 가능.   
yolo_v3-> 사용전력 220W/250W, 사용 메모리 약 1GB, Volatile GPU-Util 50%   
yolo_v4 -> 사용전력 262W/250W, 사용 메모리 약 2GB, Volatile GPU-Util 81%   





