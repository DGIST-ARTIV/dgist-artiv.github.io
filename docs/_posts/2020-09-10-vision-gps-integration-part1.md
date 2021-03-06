---
date: 2020-09-10
layout: post
title: Vision and GPS Data Integration Driving [Part 1]
subtitle: Vision과 GPS 데이터를 통합해 주행해보기 [Part 1]
description: Vision과 GPS 데이터 중 하나를 상황에 따라 선택해서 교내 주행해보기 [Part 1]
image: https://user-images.githubusercontent.com/25432456/91651414-542f5100-eac7-11ea-8c2c-66eb21f90418.gif
optimized_image: https://user-images.githubusercontent.com/25432456/91651414-542f5100-eac7-11ea-8c2c-66eb21f90418.gif
category: control
tags:
  - Control
  - Tracking
  - Vision
  - GPS
  - Integration
author: hoyeong
---

# Vision - GPS 데이터 통합 주행 Part 1. [첫 걸음]

## 기존 주행 방법
차량이 도로를 주행하려면 어떤 데이터를 이용해야할까?   
기존의 경우에는 여러 주행 방법 중에서 Computer Vision을 이용한 차선 주행을 이용하였다.   
자세한 방법은 이전 게시글에 있으니까 확인해보도록 하자.   
요약하자면 다음과 같다.   

1. 카메라로 차량의 양쪽 차선을 인식한다.
2. 중간 차선을 점들의 모임으로 얻는다.
3. 2.에서 얻은 점들을 여러가지 변환을 통해서 주행 알고리즘으로 넘겨준다.
4. Pure Pursuit을 통해서 해당 점들을 Tracking한다.

## 개선된 점?
이때까지 Vision만의 데이터로 주행하면서 조금씩 발전을 이루었다.   
원래는 Vision측에서 전달 속도를 높이기 위해서 소량의 점(10개 정도)을 제공하였다.   
이는 Pure Pursuit을 이용한 주행을 하기에는 매우 소량의 점이었다.   
그 이유는 점 사이의 간격이 크기 때문에 조그만 변화에도 큰 Steer input값이 발생하는 것이다.   
실제로 직진 구간에서 핸들이 많이 떨렸으며 분명 자율주행차라고 하기엔 많이 불안정한 상태였다.   
그래서 우리는 Tracking에 중요한 특정한 점(Target Point)을 기준으로 Point Interpolation을 사용했다.   
약 10개 정도의 점의 개수를 늘렸음에도 불구하고 핸들의 떨림은 눈에 띄게 줄어들었다.   
게다가 직진 구간에서 안정적인 모습을 보였고 PID 제어를 통해서 곡선에서도 개선을 이루었다.   

## 그렇다면 뭐가 문제?
위에서 차선을 따라가는 기능에서 개선을 이루었다고 했는데 뭐가 더 필요한가?   
차선만 따라가는 것으로는 자율주행이 완성되진 않는다.   
우리가 운전을 할 때를 생각해보자.   
차선만 따라가면 운전을 완벽하게 할 수 있는가? 오직 차선만 말이다.   
물론 사람의 경우엔 중간에 1~2초 차선이 보이지 않아도 주변 환경을 고려해 주행이 가능하다.   
하지만 기계는 그렇지 않다.   
차선이 보이지 않으면 주인님을 위해서 어떻게든 차선과 비슷한 것을 볼려고 한다.   
이것은 참 위험한 것이다...   
갑자기 인도(Not India)로 가는 차선을 제공할 수 있으며 주차장, 벽..으로 가는 차선을 만들 수 있다.   
게다가 카메라로 빛이 강하게 들어온다면?   
사람도 눈에 레이저를 쏘면 볼 수 있는 게 없는데 ㅠㅠ   
이것만 생각하면 차선만으로 주행할 수 없다는 것은 당연한 것이다.   

## 그럼 어떻게 해결할 건데?
날이 어두워도, 심지어 차선이 보이지 않아도 도로에서 주행해야 한다.   
위 경우에는 어떤 센서를 사용해야할까?   
똑!똑!한 ARTIV 친구들은 그 답을 알고 있다. 답은 GPS다.   
~~솔직히 GPS만 사기여도 어디든 갈 수 있다고 생각.~~   
우리에겐 꽤 정밀한 GPS가 있으니까 위에서 언급한 Vision의 한계를 보완할 수 있다.   

## 매우 SMART한 HD-MAP 팀!
정밀한 GPS도 모자라 똑똑한 HD-Map팀 덕분에 개발이 훨씬 쉬워졌다.   
자세한 설명은 HD-Map팀이 해주겠지만 간단히 설명하면 다음과 같다.   

1. GPS의 데이터는 일반적으로 위도 및 경도로 이루어져 있다.
2. 위도, 경도를 UTM이라는 x, y 좌표계로 표현한다.   
3. 현재 위치를 고려해 기존의 주행 경로에 포함된 점들 중, 내 앞에 있는 30개의 점들의 좌표를 준다.   

그냥 우리는 기존 Vision팀에서 제공한 점들을 따라가는 것처럼 GPS점도 따라가면 되는 것이다!   

## Vision? or GPS?
Vision이 해결 못하는 상황을 GPS 해결하여 주행 가능하다면 다음과 같은 질문이 있을 것이다.   
 "그냥 처음부터 끝까지 GPS점만 따라가면 되는거 아니에요?"   
~~Vision을 개발했는데 버릴 순 없잖아.~~   
실제로 위와 같은 고민을 하여서 Vision을 써야만 하는 이유를 ~~겨우~~ 찾아보았다...   
HD-Map측에서 주는 30개의 점들이 갱신되는 속도가 약 10fps로 매우 느리다는 것이다.   
반면 Vision측에서 주는 경로 위의 점들은 빠를 땐 무려 60fps, 아무리 느려도 10fps 이상이다.   
그렇기에 일반적으로 주행할 때는 높은 갱신 속도를 보이며 부드러운 주행이 가능한 Vision 데이터를 이용한다.   
그러다가 만약 Vision이 좋지 않은 성능을 보이는 경우에는 GPS 데이터를 이용해 주행을 보완한다.   
그렇다면 Vision과 GPS의 데이터를 번갈아가며 사용하면 완벽에 가까운 주행이 되겠지?   

실제 알고리즘과 결과는 다음 글에서 계속...
