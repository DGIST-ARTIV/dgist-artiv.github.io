---
date: 2020-08-26
layout: post
title: Local path planner [part1]
subtitle: local path planner
description: state lattice를 활용한 local path planner 활용 
image: https://user-images.githubusercontent.com/25432456/91651414-542f5100-eac7-11ea-8c2c-66eb21f90418.gif
optimized_image: https://user-images.githubusercontent.com/25432456/91651414-542f5100-eac7-11ea-8c2c-66eb21f90418.gif
category: control
tags:
  - Lattice planner
  - Control planning
  - Planning Algorithm
author: MinJong Kim
---

# Local Path Planning을 위한 Model Predictive Trajectory Generator

## Local Path Planning이란?
local path planning은 Global Path가 주어지거나, 국소적 목표가 설정되어있을때, 국소적인 경로를 계획하는 path planner이다. 회피주행, 편향주행에 사용된다.

## Local Path Planning을 하는 이유
AV(Autonomous Vehicle)이 주행시 도로에서 다양한 상황을 맞이할 수 있다. 예들들어 공사중으로 인해 라바콘이 도로 한복판을 가로막고 있다던가 불법 주차로 인해 도로의 절반을 차량이 가로막고 있는경우가 대표적이다.
![공사중 도로](https://github.com/DGIST-ARTIV/dgist-artiv.github.io/blob/master/docs/media/load1.jpeg)

![불법주차 도로](https://github.com/DGIST-ARTIV/dgist-artiv.github.io/blob/master/docs/media/load2.jpeg)


이 경우 차량이 정지후 그 상황이 종료된 후 다시 Global Path를 따라가도 되지만, 위와 같은 경우 그 상황이 언제 해소될지 모른다.따라서 이런상황들을 유연하게 대처하기 위해 Local Pathplanning이 필요하다.

## 왜 Model predictive trajectory generator(MPTG)를 선택하였는가??
A*, Dijkstra등 다양한 path planning Algorithm이 존재하지만, local path planning 알고리즘으로 MPTG를 설정한 이유는 다음과 같다.
1. 차량의 상태를 예측하여 경로를 생성하기 때문에 차량이 따라가기 적합한 경로를 생성한다.
2. Sampling State가 적은경우 빠른 연산이 가능하다.

### Model predictive trajectory generator에 대한 설
