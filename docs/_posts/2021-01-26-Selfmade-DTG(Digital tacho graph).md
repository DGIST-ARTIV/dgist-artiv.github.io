---
date: 2021-01-27
layout: post
title: Selfmade DTG(WIP)
subtitle: rosbag을 이용하여 차량의 주행정보를 계산하기!
description: 자율주행 임시운행 허가를 위한 DTG 만들기
image: https://user-images.githubusercontent.com/59161083/105984936-2b46d280-60de-11eb-9411-8a07a1ea9b79.png
category: vision
tags:
  - automation
  - rosbag analysis
  - rosbag analysis with Window
  - Data Analysis
author: gu
---
Author : 이  구 <br/>
Date: 2021.01.27

<p align="center"><img src="https://user-images.githubusercontent.com/59161083/100884067-61a80d00-34f4-11eb-9633-f46dd25824f5.png" width="100%" height="100%"></p>   

## Environment Setting
Bagpy must be installed.

```
pip3 install bagpy
```

## Why?
우리는 현재, 지난 1년간 개발한 IONIQ을 이용하여 자율주행차량 임시운행허가를 받기위해 준비중이다.
이를 위한 필수조건 중 하나로, 주행기록계(DTG)를 설치하여야 한다. 그 근거는 아래와 같다.

- 자율주행자동차의 안전운행요건 및 시험운행 등에 관한 규정 제 17조 (운행기록장치 등)
   자율주행자동차에는「교통안전법」제55조제1항에 따른 운행기록장치를 장착하여야 하고, 운행기록장치 또는 별도의 기록장치에 다음 각 호의 항목을 저장하여야 한다.     
		- 1. 자율주행시스템의 작동모드 확인   
		- 2. 제동장치 및 가속제어장치의 조종장치 작동상태   
		- 3. 조향핸들 각도   
		- 4. 자동변속장치 조종레버의 위치   

이를 위해, IONIQ 차량에 운행기록계를 설치하기 위해 다양한 업체들에 연락한 끝에 설치할 수 있었지만, 위의 법령에 해당하는 모든 정보를 기록해주지는 않았다.
또, 이와 별개로, 자율주행임시운행 허가를 위한 제출서류 중 하나인, 사전시험주행보고서의 항목들에 해당하는 정보(Auto/Manual 모드 별 주행거리, 평균 속도 등)들 역시 얻을 수 있어야 했다. 
그래서, 우리의 자체 차량 ROS 통신 프로토콜 IONIQ INFO를 이용하여 위의 정보들을 직접 뽑아내기로 했다.
IONIQ의 통신 프로토콜 메뉴얼은 [여기](https://docs.google.com/document/d/1Mvyvs1Tt20U99uA4o_h4c2-KB7s64NOQz6vd_-SGwh4/edit)서 확인할 수 있다.

## How?
bagpy를 이용하면, Window 환경에서도 rosbag 파일을 쉽게 분석할 수 있다.
자세한 사용법은 [공식 docs](https://jmscslgroup.github.io/bagpy/)를 참고하자. 

코드를 짜기 전에, 어떤 정보를 얻어야 할지 미리 생각해보자.



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
