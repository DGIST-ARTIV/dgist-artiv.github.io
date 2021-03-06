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
CULane 데이터셋뿐만 아니라 이러한 형식을 가진 한국 도심 데이터셋이 필요했다. labelme 등 여러 annotation tool을 이용해보았는데 원하는 tool을 찾지 못해서 annotation tool을 직접 제작하기로 했다.
ENet-SAD 논문 저자가 사용한 CULane 데이터셋 파일구조와 segmentation 방식([seg_label_generate](https://github.com/XingangPan/seg_label_generate) 참고)을 맞춰 제작했다.
파일을 video 이름으로 나눔으로써 자동으로 사용한 video가 구분되었다. CULane 데이터셋 파일 구조는 다음과 같다.
```
Culane
  ├─ video path
    ├─ 0000.jpg ##image
    ├─ 0000.txt ##text file include the ground truth of image
    ├─ ... ##a lot of jpg and txt pairs
  ├─ list
    ├─ train.txt ## all image paths
    ├─ train_gt.txt ## all segmentation image paths and the label paths
  ├─ laneseg
    ├─ video path
       ├─ 0000.png ##segmentation images
```

### annotation tool의 핵심 기능
- 동영상의 화면(이미지) 하나하나를 띄워주고 저장하는 기능
- 차선 4개를 구분짓는 기능
- 띄운 이미지에 마우스 클릭 시 x좌표와 y좌표(ground truth가 될 지점)를 text 파일에 쓰고 저장하는 기능
- annotation한 이미지들에 대한 segmentation image을 생성하고 저장하는 기능
- 어떤 이미지들이 있는지 파일 경로와 ground truth가 저장된 text 파일의 경로를 text 파일에 저장하는 기능

### 시각적으로 annotation하기 용이하도록 도움을 줄 기능
- 띄운 이미지에 마우스 클릭 시 점이 그려지도록 하는 기능
- 현재 몇번째 차선의 점을 찍고 있는지 알려줄 수 있는 legend 제작

### 추가 기능
- 직선 화면에서는 거의 차선의 위치가 바뀌지 않아 같은 ground truth를 이용해도 되는 경우기 존재함 --> 전 이미지의 ground truth를 담아 두는 기능
- 잘못 찍은 점 하나를 지우는 기능
- 이미지에 찍은 모든 점들을 지우는 기능

### annotation tool 다운로드
곧 오픈 예정

### 직접 만든 dataset 학습시켜보자
직접 만든 dataset이 잘 학습되는지 test를 해보았다. 
빠르게 annotation해서 datset 몇 장을 만들고 잘 학습되는 것을 보았다.
성능향상을 위해 annotation을 많이 하는 것이 필요하다.
우선 이틀에 걸쳐 낮 영상과 밤 영상에서 총 1000장 정도의 frame을 annotation했다.  
culane dataset과 합쳐 ENet-SAD train을 시켰다.  
다음은 culane dataset만을 이용해 test한 결과이다.  
![only_culane-_online-video-cutter com_](https://user-images.githubusercontent.com/53460541/86783514-5ebb0480-c09b-11ea-987d-0871040c6a19.gif)  
다음은 우리가 annotation한 dataset을 포함시킨 결과이다.
![culane_and_ours-_online-video-cutter com_](https://user-images.githubusercontent.com/53460541/86783519-61b5f500-c09b-11ea-92e5-c876b145ea8c.gif)  
아직까지 큰 차이는 없어보이지만 더 많은 dataset을 만들게 되면 더 좋은 성능을 가지게 될 것이다.
