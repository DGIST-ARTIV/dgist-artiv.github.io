---
date: 2020-06-24
layout: post
title: Lane annotation tool
subtitle: making dataset in the same format as CULane 
description: making dataset in the same format as CULane
image: https://user-images.githubusercontent.com/53460541/85832796-af914a00-b7cb-11ea-84e0-4e77ce35949c.png
optimized_image: https://user-images.githubusercontent.com/53460541/85832796-af914a00-b7cb-11ea-84e0-4e77ce35949c.png
category: vision
tags:
  - vision
author: eunbin
---

# 차선인식을 하기 위한 dataset 생성하기(Creating annotation tool for good performance)

### 딥러닝 네트워크를 사용하는데...
차선인식을 하기 위한 방법으로 딥러닝을 이용하기로 했다. 딥러닝 네트워크가 수 많은 parameter를 학습할 때, 양질의 데이터셋을 많은 것이 딥러닝 네트워크 성능 향상에 핵심적이다.  
논문에서 평가용으로 많이 사용하고 있는 lane open dataset은 CULane, TuSimple, BDD100K이 있다. 이 중 ENet-SAD에 넣을 데이터셋은 CULane으로 
