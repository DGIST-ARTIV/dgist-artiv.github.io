---
date: 2020-07-02
layout: post
title: Probabilistic lane estimation from ENet-SAD's result
subtitle: How to draw probline from points
description: How to draw probline from points
image:![draw5](https://user-images.githubusercontent.com/53460541/86770720-1516ed80-c08c-11ea-8d3e-da22733bf592.gif)
category: vision
tags:
  - vision
  - probline
  - lane detection
author: eunbin
---

## Probline
ENet-SAD가 50 frame이 나오는 것을 확인했고, 이제 차선이 있을 것 같다는 probline 그려 표시하기로 했다.  
ENet-SAD가 100% 정확하게 차선이 있다는 것을 알아맞추는 것이 아니기 때문에 outlier들이 많았다.  
-> outlier 처리를 통한 성능 향상  
-> 원내 dataset을 학습을 통한 성능 향상  
두 가지를 목표로 5일간 probline과 annotation을 진행했다. 여기서는 probline에 대한 이야기만 나눌 예정이다.

4개의 차선 즉, leftleft, left, right, rightright 차선을 그려주기로 했다. 총 5차례의 시도에 거쳐 어느 정도 안정화시켰다.  
#### 1. ENet-SAD에서 나온 probability list의 점들을 모두 이어보았다.  
![draw1](https://user-images.githubusercontent.com/53460541/86770711-11836680-c08c-11ea-96ba-70c0a6a2e870.gif)  
결과는 당연히 좋지 않았다. 처음엔 probline이니까 차선이 있을 것 같은 점들을 모두 이으면 된다고 생각했는데, outlier가 있음을 간과했다.  

#### 2. linear regression을 통해 outlier를 처리해보았다.  
![draw2_1](https://user-images.githubusercontent.com/53460541/86770710-10ead000-c08c-11ea-9b92-efa07af69d05.gif)  
outlier를 모두 무시했으나 두 가지 문제점이 있다.  
- linear regression이다 보니 곡선이 나왔을 경우 처리하지 못한다.  
- outlier가 주를 이룰 때에도 선을 그려 엉망진창이 된다.  

#### 3. 곡선의 문제를 해결하기 위해 polynomial regression을 해보자.  
![draw3](https://user-images.githubusercontent.com/53460541/86770715-12b49380-c08c-11ea-984d-b32d3c5d0450.gif)  
결과가 나쁘진 않았다. 하지만 여전히 outlier를 해결하지 못하는 결과를 볼 수 있다.  
이를 해결하기 위해서는 머리를 조금 더 써야할 것 같다.  
  
#### 4. outlier를 처리하기 위해 ransac을 적용시켰다.  
![draw4](https://user-images.githubusercontent.com/53460541/86770717-13e5c080-c08c-11ea-92e2-7ac5ff6db89a.gif)  
ransac은 random sample consensus의 약자로, 가장 많은 수의 데이터들로부터 지지받는 모델을 선택한다. least square method보다 더 효과적인 모델을 만들어내기도한다.  
하지만 우리가 처리하는 data들의 양이 많지 않다. 가장 많은 수가 어떤 값을 가리키고 있는지 명확하지 않다.  
inlier가 주를 이루고 있을 때가 존재해 ransac으로 몇몇 점들은 무시하지만 outlier가 주를 이루고 있을 땐 inlier를 outlier로 취급해버린다.  
또한, for문을 돌면서 모델을 만들 sample들을 선택하기 때문에 시간도 오래걸린다. 조작해야하는 parameter 수가 많은 것도 문제였다.  
그 결과로, 좋은 성능을 이끌어내기 어려웠다. 더 좋은 성능을 내는 알고리즘을 고민해봐야한다.  

#### 5. start point를 잡고 각도 범위 밖이면 삭제하는 알고리즘 적용시켰다.  
![draw5](https://user-images.githubusercontent.com/53460541/86770720-1516ed80-c08c-11ea-8d3e-da22733bf592.gif)  
위의 영상들과 달리 안정적인 것을 볼 수 있다. 각 차선마다 나올 수 있는 각도를 계산해 각도의 범위로 outlier를 제거했다.  

--> 개선시켜야할 점은 여전히 존재한다.
- 차가 가운데로 항상 다니는 것이 아니기 때문에 start point가 항상 바뀐다. start point 초기값을 어떻게 잡을지 또 다른 알고리즘을 통해 결정해야한다.
- 이 방법을 통해 line을 그릴 시에 0.01초 정도 걸린다. 계산 가속화/최적화를 하기 위한 방법을 모색해야한다.
