---
date: 2020-06-24
layout: post
title: Lane annotation tool
subtitle: making dataset in the same format as CULane 
description: making dataset in the same format as CULane
image: https://user-images.githubusercontent.com/53460541/85832796-af914a00-b7cb-11ea-84e0-4e77ce35949c.png
category: vision
tags:
  - vision
  - annotation
author: eunbin
---

## 차선인식을 하기 위한 dataset 생성하기(Creating annotation tool for good performance)

### 딥러닝 네트워크를 사용하는데...
차선인식을 하기 위한 방법으로 딥러닝을 이용하기로 했다. 딥러닝 네트워크가 수 많은 parameter를 학습할 때, 양질의 데이터셋을 많은 것이 딥러닝 네트워크 성능 향상에 핵심적이다.  
논문에서 평가용으로 많이 사용하고 있는 lane open dataset은 CULane, TuSimple, BDD100K이 있다. 이 중 테스트 결과가 제일 좋은 CULane을 이용하기로 했다.
CULane 데이터셋뿐만 아니라 이러한 형식을 가진 한국 도심 데이터셋이 필요했다. 여러 annotation tool을 이용해보았는데 괜찮은 tool을 찾지 못해서 annotation tool을 직접 제작하기로 했다.
ENet-SAD 저자가 사용한 CULane 데이터셋 형식으로 맞춰 제작했다. CULane 형식은 차 기준으로 왼쪽과 오른쪽 각각 2개의 차선까지 ground truth로 제공한다. 

### annotation tool의 핵심 기능
- 동영상의 화면(이미지) 하나하나를 띄워주고 저장하는 기능
- 차선 4개를 구분해서 
- 띄운 이미지에 마우스 클릭 시 x좌표와 y좌표(ground truth가 될 지점)를 text 파일에 쓰고 저장하는 기능
- annotation한 이미지들에 대한 segmentation image을 생성하고 저장하는 기능
- 어떤 이미지들이 있는지 파일 경로와 ground truth가 저장된 text 파일의 경로를 text 파일에 저장하는 기능

### 시각적으로 annotation하기 용이하도록 도움을 줄 기능
- 띄운 이미지에 마우스 클릭 시 점이 그려지도록 하는 기능
- 현재 몇번째 차선의 점을 찍고 있는지 알려줄 수 있는 legend 제작
