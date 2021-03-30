---
date: 2020-08-11
layout: post
title: What happens to lane detection when a car changes lanes? (Augmentation)
subtitle: Augmentation for lane detection
description: Augmentation for lane detection
image: /assets/img/car_on_center_line.png
category: vision
tags:
  - vision
  - autonomous
  - augmentation
  - dgist
author: eunbin
---

## 차가 중앙선 위에 위치한다면? (문제점)
dataset을 더 만들어내기 위하여 annotation tool을 직접 개발하였다. [이전 게시글](https://dgist-artiv.github.io/vision/2020/06/24/Lane_annotation_tool.html)
직접 차에 카메라를 달고 annotation할 영상을 찍었다. 
차선을 변경할 때 변경하는 쪽의 차선의 ground truth를 어떻게 할지 결정하지 않아서 제외하고 annotation을 했는데
아니나 다를까 차선이 차 바로 앞에 있다는 상황에 대한 학습이 안되어있으니 lane detection이 전혀 되지 않았다.
우리는 현재 가지고 있는 데이터셋을 이용하여 이런 상황을 해결하고자 했다.


## 해결 방법 제시
가지고 있는 데이터의 대부분은 차로의 중앙을 맞춰 주행하는 이미지들이었다. 이런 이미지를 shifting하면??
왼쪽 차선이나 오른쪽 차선이 차의 중앙에 위치하는 것처럼 보일까??
shifting은 차와 차선의 위치관계가 변하지 않도록 조심해야한다.

## augmentation 이미지 만들기
왼쪽에 공간을 만들어 왼쪽차선이 차의 중앙으로 오게 하는 경우와 오른쪽에 공간을 만들어 오른쪽차선이 차의 중앙으로 오게 하는 경우 두 가지로 나누어 이미지를 생성했다.
1. annotation했던 이미지에 왼쪽 차선이 존재하는지 확인
2. shifting할 크기 정하기

> 왼쪽 공간 크기 정하기: 
> 
>  - 왼쪽 차선의 x축 좌표들의 평균이 중앙으로 떨어진 거리 만큼 오른쪽으로 이동하면 왼쪽 차선의 중간지점이 이미지 중앙으로 이동
>  - 왼쪽 차선이 0~10 사이 random만큼 오른쪽으로 이미지 픽셀 이동
>  - 이를 역산하면 왼쪽 공간 크기 계산 가능
``` python3
# 이미지의 중앙 지점이 400이라고 하면 코드는 다음과 같다.
left_space = int(400-(sum(left_lane_x, 0.0)/len(x_left_lane_x))+random.randint(0,10))
```

|![shifting_method](https://user-images.githubusercontent.com/53460541/112950973-d6731700-9175-11eb-977f-ed66be91dd41.png)|
|:---:|
|왼쪽 차선이 얼만큼 어디로 이동하는지 이해를 돕기 위한 이미지|

> 오른쪽 공간 크기 정하기:
>
> 왼쪽 공간 정하는 방식이랑 비슷하다. 왼쪽은 x축으로 더해주는 것이고 오른쪽은 x축으로 빼주는 것이어서 기준이 다른다. 
> 오른쪽 차선의 x축 좌표들의 평균이 이미지 width와 떨어진 거리만큼 왼쪽으로 이동하면 오른쪽 차선의 중간지점이 이미지 중앙으로 이동
> 오른쪽 차선이 0~10 사이 random만큼 왼쪽으로 이미지 픽셀 이동
> 역산하면 오른쪽 공간 크기 계산 가능

``` python3
# 이미지의 width가 800이라고 하면
right_space = int(800-(sum(right_lane_x, 0.0)/len(right_lane_x))+random.randint(0,10))
```

3. 모든 이미지 픽셀을 정한 shifting 크기만큼 이동하고 annotation 했던 좌표들 이동, 그 좌표에 맞는 segmentation 이미지 생성
5. 원래 이미지 크기에 맞춰 빠져나간 이미지 잘라내기
6. 빈 공간은 이미지 전체의 pixel 정보(RGB 값)의 평균으로 채워넣기

최종적으로 생성된 이미지는 다음과 같다.

|![51](https://user-images.githubusercontent.com/53460541/112949963-b8f17d80-9174-11eb-937f-2ba0510f7da4.jpg)|
|:---:|
|왼쪽에 공간을 만들어 오른쪽으로 shifting한 이미지|
|![51](https://user-images.githubusercontent.com/53460541/112950051-d1fa2e80-9174-11eb-9bfd-bef50902528e.jpg)|
|:---:|
|오른쪽에 공간을 만들어 왼쪽으로 shifting한 이미지|

## 결과
아쉽게도 성능이 크게 개선되지는 않았다. 차가 차선 위에 있게되면, 밑에 있는 이미지처럼 가운데 차선은 거의 기울어지지 않은 상태로 보인다.
그런데 학습한 이미지는 왼쪽 혹은 오른쪽 차선이니 기울어져 있을 수 밖에 없다.
곡선에서 영향을 줄 수는 있어도 차의 조향이 잘못되어 차선 위에 차가 있게되면 여전히 인식이 잘 되지 않는다.
augmentation을 이용하여 학습시키는 데이터셋에 변화를 주어 차선 인식 단계에서 해결하고자 한 것은 좋은 접근이라고 생각한다.
차선 인식 단계에서 해결하게 되면 후처리 하는데 드는 computational cost를 줄여 runtime을 줄일 수 있다.
성능이 크게 개선되지 않아 후처리로 해결해야겠다.
![center](https://user-images.githubusercontent.com/53460541/112952560-957c0200-9177-11eb-9e84-3727f9ce9292.png)
