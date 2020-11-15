---
date: 2020-11-15 20:04:34
layout: post
title: Lidar Segmentation & Tracking
subtitle: 라이다 인식 및 추적
description: 라이다 인식 및 추적
image: https://user-images.githubusercontent.com/59762212/99184715-529d2d00-2788-11eb-8c1e-ff6be33e5554.png
category: lidar
tags:
  - autonomous
  - segmentation
  - tracking
  - deep_learning
  - lidar
author: jaeseung
---
안녕하세요 연구원 양재승입니다. 자율주행을 위해 Lidar를 이용한 Segmentation 및 Tracking에 대해 작성하고자 합니다.  
자율주행을 위해서는 주변 상황에 대한 정보를 최대한 많이 알아야합니다. 이를 위해 레이저 펄스를 쏘고 반사되어 돌아오는 시간을 측정하여 반사체의 위치좌표를 측정하는 레이다 시스템인 Lidar라는 센서를 사용합니다. 이러한 점들을 이용하여 주변에 있는 물체가 어디에 위치하는지, 어떤 속도를 가지는지, 크기는 어느정도인지 알 수 있어야 주행을 위한 데이터로 활용이 가능합니다.  
  

> Lidar에서 넘어오는 점이 많기에 어느정도 Downsampling을 거쳐 알고리즘을 구현하였습니다.  
  
  
먼저 Segmentation을 하기 위해 간단한 Machine Learning을 사용하는데, K-means 알고리즘을 사용하였습니다.  

<p align="center">
<img src="https://user-images.githubusercontent.com/59762212/99187032-89c70a80-2797-11eb-8252-609cfa3f0f32.png" width="400">
</p>

초기에 설정한 점의 개수로 물체의 중앙이라고 판단되는 점들을 찾아 같은 물체를 나타내는 점이라고 판단되는 주변의 가까운 점들을 포함해 나갑니다. 이때 최적이라고 생각되는 중심이 계속 바뀌며, 더이상 점들 주변에 가까운 점이 없다면 연산을 그만두고 하나의 물체로 판단을 합니다. 이러한 방식을 반복하여 Roi(Region of interest) 내 물체라고 생각되는 점군을 나눈 후, 또 한 번의 가까운 거리에 있는 점군끼리 merge할 것인가에 대해 연산하여 최종 segmentation 결과를 도출합니다.

한편 현재 사용하고 있는 라이다는 16채널의 저채널 모델로, segmentation 결과 중 원거리로 갈수록 채널의 ring간 간격이 넓어져 같은 물체지만 채널마다 다른 물체로 인식하는 현상이 발생하였습니다. 이에 거리에 대한 클러스터링 가중치를 별도로 부가하여 거리별 segmentation 성능이 일정하게 나오도록 설계하였습니다. 이 방법은 분명 원거리에서 물체의 GT(Ground Truth)와 실제로 차이가 나는 결과가 나올 수 있으나, 이는 의도된 설계입니다. 센서 퓨전을 위한 개발된 저채널용 알고리즘입니다.  

<p align="center">
<img src="https://user-images.githubusercontent.com/59762212/99184715-529d2d00-2788-11eb-8c1e-ff6be33e5554.png" width="600">
</p>  

다음은 Tracking에 대한 설명입니다. Tracking을 하기 위해서 Sementation 결과를 이용하여 Kalman filter를 사용하였습니다.  

<p align="center">
<img src="https://user-images.githubusercontent.com/59762212/99184575-495f9080-2787-11eb-8ebd-0b1049633ac5.png" width="400">
</p>  

Kalman filter는 로봇의 state를 추정하기 위해 가장 흔히 사용되는 방법입니다. 주변 물체들을 Kalman filter의 prediction step과 correction step을 통해 움직이는 물체를 추적하였고, 이를 통해 물체가 움직인 경로를 알 수 있습니다. 경로를 이용하여 각 물체의 속도와 같은 움직임을 도출했습니다. 이 방식을 사용하면 불규칙한 input data에도 비교적 정확한 output data를 얻을 수 있습니다. 

왼쪽은 Tracking 결과, 오른쪽은 카메라로 찍은 결과입니다.  

<p align="center">
<img src="https://user-images.githubusercontent.com/59762212/99185054-9f820300-278a-11eb-9897-7285528d8fe5.png" width="650">
</p>  

