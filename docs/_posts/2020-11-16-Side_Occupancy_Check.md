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
  - ROS
  - ros melodic
  - depth
  - depth estimation
  - monodepth2
author: gu
---
Author : 이  구 <br/>
Date: 2020.11.16

## Environment Setting
Tensorflow: 1.14.0   
Keras: 2.3.1   
OpenCV: 4.2.0   
GPU: RTX 2080Ti x 2   

## Why?
차선 변경과 교차로에서의 안전한 주행을 위해, 차량 양측면의 장애물 존재 여부를 알아야 했다.   
카메라는 루프에, 라이다는 범퍼 가운데에 설치되어 있는데 옆은 어떻게 볼까?
차에 새로운 카메라를 달아주기로 했다.   

## Sensor Specification
**Camera:** logitech c920e   
**Lens:** Wide-angle lens for smartphones

쉽게 구할 수 있는 웹캠에 광각 렌즈를 붙여서 사용하였다.
<p align="center"><img src="https://user-images.githubusercontent.com/59161083/99421650-f54cdb80-2941-11eb-9b3c-71db246c64b9.jpg" width="50%" height="30%"></img></p>

## How?
차량의 양측면에 부착한 카메라를 통해 얻은 이미지를 이용하여, 차량 양측면의 Occupancy 정보를 얻어야 한다.
이를 위해, 아래와 같은 

<p align="center"><img src="https://user-images.githubusercontent.com/59161083/99424497-15ca6500-2945-11eb-81f5-c5f54d2d712f.PNG" width="70%" height="70%"></img></p>




### 해상도에 따른 성능 변화

| resolution | fps |   
|:--------:|:--------:|   
| 720 x 480 | 약 36 fps |   
| 1080 x 720 | 약 23 fps |   
| 1920 x 1080 | 약 8 fps |   
 

## Improvement
### 기존의 ROS node graph
기존에 사용한 방식의 ros node graph는 아래와 같다. 
<p align="center"><img src="https://user-images.githubusercontent.com/59161083/87303410-18a7ea00-c54e-11ea-9a1d-4802df70a766.png" width="150%" height="150%"></img></p>

기존의 방식에서 이미지 토픽을 몇 번 Publish 하게되는지 확인해보자.     
```/usb_cam```에서 ```/detector_manager```로 */usb_cam/image_raw* topic을 **한번**, ```/detector_manager```에서 ```/DetectedImg```로 *yolov3/image_raw* topic을 **한번**, 마지막으로 ```/DetectedImg```에서 */YOLO_RESULT* topic을 **한번** publish 하게 된다. 

usb_cam을 통해 받은 이미지를, monodepth2로 depth map을 얻기 위해,```/usb_cam/image_raw```에서 ```/DepthMap```으로 Image topic을 한번 더 받아오게 된다. 아직 구현하지는 못했지만, 이미지의 최종 정보에 DepthMap을 사용하여 추정한 거리 정보를 포함시킬 것이기 때문에, 최소 한번 이상의 추가적인 publish가 필요할 것이다.

이 점을 고려하면, **이미지 한 장**이 노드 사이를 **5번 이상** 이동하게 되는 것이다. 이때, 동시에 처리하는 데이터의 양이 늘어날수록(bandwidth가 높아질수록), ROS 자체의 delay가 심해진다.  
이로인해, 전체적인 시스템을 수정하게 되었다.

### 수정된 ROS node graph

<p align="center"><img src="https://user-images.githubusercontent.com/59161083/87302562-8eab5180-c54c-11ea-9f3b-ee6c451d616e.png" width="150%" height="150%"></img></p>

새롭게 수정된 방식에서는, ```/usb_cam```에서 ```/detector_manager```로 */usb_cam/image_raw* topic을 **한번**, ```/detector_manager```에서 ```/PostProcessing```으로 */yolov3/image_raw* topic을 **한번** publish 한 후, 한 노드 안에서 detect 결과의 시각화와 depth map 추정이 동시에 이루어지게 된다.    

## TO DO
0. ~~ROS Image topic을 받은 후, Depth 정보를 Image로 Publish~~ (20.07.11)   
0. ~~ROS를 사용하는 경우 심한 delay 발생...~~ (20.07.13)
0. ~~DepthMap과 Bbox를 이용하여 인식된 객체의 거리 추정하기~~ (20.07.14)
0. ~~DepthMap estimation 결과를 출력하는 과정에서 normalization 없애기~~ (20.07.14)
0. ~~인식된 객체의 실제 위치와 DepthMap의 Bbox pixel값 평균 비교하기~~ (20.07.16)





