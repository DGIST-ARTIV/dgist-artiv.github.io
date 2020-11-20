---
date: 2020-11-04
layout: post
title: Vision and GPS Data Integration Driving [Part 3]
subtitle: Vision과 GPS 데이터를 통합해 주행해보기 [Part 3]
description: Vision과 GPS 데이터 중 하나를 상황에 따라 선택해서 교내 주행해보기 [Part 3]
image: /assets/img/bright_vision_gps_integ_gif.gif
optimized_image: /assets/img/bright_vision_gps_integ_gif.gif
category: control
tags:
  - Control
  - Tracking
  - Vision
  - GPS
  - Integration
author: hoyeong
---

# Vision - GPS 데이터 통합 주행 Part 3. [Mode Switching]

# 지난 이야기
Vision과 GPS가 정보를 교차해야 하는 시점을 파악하는 방법을 두 가지 생각했다.   
Method 1, 2라고 결정했고 이번 글에서는 Method 1을 적용한 결과를 보여줄 것이다.   
Method 1을 요약하자면, GPS는 거의 모든 구간에서 안정적이라고 가정하고, Vision의 안정성만 판단한다.    
Vision의 안정성은 Pixel 단위로 얻은 데이터를 기준으로 판단한다.    
만약 Vision이 불안정하다면 그 즉시 GPS를 기반으로 주행을 대체한다.   
원래는 나의 Pure Pursuit과 승기의 Stanley 알고리즘을 합치려고 했다.    
하지만 빠른 시간 안에 알고리즘이 서로 교체되어야 하기 때문에 Pure Pursuit 하나만 사용하기로 결정했다.    
지난 이야기에서 말한대로, 캠퍼스를 나가면 무용지물이 될 수 있는 알고리즘이기에 조심해야 한다.   
하지만 우리는 셔틀 경로 주행이 최우선 목표이기 때문에, Method 1을 우선 적용했다.   

# 안정성 판단 알고리즘 설명
미리 말하자면 안정성 판단 알고리즘은 주행 중 실시간 Pure Pursuit 결과를 보면서 떠오른 것이기 때문에 매우 간단하다.   
알고리즘의 안정성 판단 순서는 다음과 같다.   

1. Vision, GPS 기반 데이터를 OpenCV에 표시한다.
2. Vision, GPS 기반 데이터의 x 값의 차이(del_x)를 구한다.
3. del_x가 일정 Pixel 이하라면 둘 다 안정하다고 판단하고 Vision 우선 모드로 주행한다.
4. 만약 del_x가 일정 Pixel 이상이라면 둘 중 하나는 불안정하다고 판단하고 순서 5로 넘어간다.
5. 화면 상의 중심으로부터 Vision, GPS 기반 데이터 중에서 가까이 있는 것을 기준으로 주행한다.

위의 과정을 보다시피 안정성 판단 알고리즘은 매우 간단하며,  잘 될 것인지도 의문이 든다.    
아래 영상으로 결과를 확인해보자.

# 결과
<iframe width="560" height="315" src="https://youtu.be/glT-78iVvrE" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>    
<테스트(1)(낮) 영상>    
<iframe width="560" height="315" src="https://youtu.be/8BloStzW0PA" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
<테스트(2)(밤) 영상>    
이전에 낮과 밤 테스트 중에 서로 성능 차이를 보였다.    
그래서 낮 주행 데이터를 추가적으로 모아 학습을 시킨 weight 파일로 다시 진행하였다.    
결과는 낮에서도 차선 인식 성능이 준수하였고, 밤과 큰 성능 차이가 없었다.    
<테스트(1)>과 <테스트(2)>를 보면 중간에 차선이 끊기는 곳에서도 큰 문제 없이 주행을 계속한다.    
실제로 차선이 존재할 때는 Vision 기반으로 주행을 하다가 차선이 끊기는 곳에서는 잠시 GPS 기반 주행으로 바뀐다.    
알고리즘은 매우 간단했지만, 성능은 그리 나쁘지 않았다.    

# 문제점
알고리즘 설명란을 보다시피 매우 간단한 알고리즘이기 때문에 언제 위험한 상황이 생길지 모른다.    
예를 들면, Vision, GPS 데이터 모두 불안정하다면 어떤 데이터를 믿을지 모르게 된다.    
두 데이터에 의한 경로가 인접해 있지만 이상한 방향을 가리킬 때, 컴퓨터는 정상이라고 판단한다.    
즉 컴퓨터는 데이터가 안정적인지, 올바른지 모르고 그냥 찍힌 경로대로만 간다는 것이다.    
이처럼 기계가 판단하기 어려운 불안정성을 구분하는 척도는 어떤 것들이 있을까?
