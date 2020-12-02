---
date: 2020-11-16
layout: post
title: Side Occupancy Check
subtitle: 차량 양측면의 장애물 여부 판단
description: 차량 양측면의 장애물 여부 판단
image: /assets/img/SideOccupancy_resize.gif
category: vision
tags:
  - autonomous
  - vision
  - Deep Learning
  - DL
  - ros melodic
  - lane change
author: gu
---
Author : 이  구 <br/>
Date: 2020.11.16

<p align="center"><img src="https://user-images.githubusercontent.com/59161083/100884067-61a80d00-34f4-11eb-9633-f46dd25824f5.png" width="100%" height="100%"></p>   

## Environment Setting
Tensorflow: 1.14.0   
Keras: 2.3.1   
OpenCV: 4.2.0   
GPU: RTX 2080Ti x 2   

## Why?
차선 변경과 교차로에서의 안전한 주행을 위해, 차량 양측면의 장애물 존재 여부를 알아야 했다.   
카메라는 루프에, 라이다는 범퍼 가운데에 설치되어 있는데 옆은 어떻게 볼까?   
차에 새로운 카메라를 달아주기로 했다!   

## Sensor Specification
**Camera:** logitech c920e   
**Lens:** Wide-angle lens for smartphones

쉽게 구할 수 있는 웹캠에 광각 렌즈를 붙여서 사용하였다.
<p align="center"><img src="https://user-images.githubusercontent.com/59161083/99421650-f54cdb80-2941-11eb-9b3c-71db246c64b9.jpg" width="50%" height="30%"></p>   

## How?
차량의 양측면에 부착한 카메라를 통해 얻은 이미지를 이용하여, 차량 양측면의 Occupancy 정보를 얻어야 한다.
이를 위해, 아래와 같은 구조의 네트워크를 사용하였다.
<p align="center"><img src="https://user-images.githubusercontent.com/59161083/99424497-15ca6500-2945-11eb-81f5-c5f54d2d712f.PNG" width="200%" height="200%"></p>   

이를 사용한 결과는 아래와 같다. 모델의 output이 0.5보다 크면 OPEN, 0.5보다 작으면 BLOCK으로 표시하였다.   

<p align="center"><img src="/assets/img/SideOccupancy_resize.gif" width="150%" height="150%"></p>   

이제, 양측면 카메라의 이미지를 하나의 모델로 추론해보자.   
가시성을 높이기 위해 OPEN인 경우 초록색, BLOCK인 경우 빨간색으로 표시하였다.    
아래의 영상은 테스트를 위해 서로 다른 두 영상을 이용한 결과이다.   

<p align="center"><img src="https://user-images.githubusercontent.com/59161083/99703225-30821280-2ada-11eb-985b-81e066cb537c.gif" width="200%" height="200%"></p> 

## ROS Application
차량의 왼쪽, 오른쪽 Occupancy를 확인한 후, 그 결과를 ROS의 Int16 형태로 publish한다.
각 토픽의 이름은 아래와 같다.
```
/SideOccupancy/Left
/SideOccupancy/Right
```
각 토픽의 메세지는 BLOCK인 경우 0, OPEN인 경우 1의 값을 갖는다.

