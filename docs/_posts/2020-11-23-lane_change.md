---
date: 2020-11-23
layout: post
title: How to change lane using vision
subtitle: Lane change
description: Lane change
image: /assets/img/lane_change.gif
category: vision
tags:
  - vision
  - autonomous
  - lane change
  - dgist
author: eunbin
---

자율주행에 있어 필요한 기능 중 하나인 차선 변경을 하기로 했다.
셔틀 경로에서 차선을 변경해야 하는 구간이 2군데가 있고,
차선변경을 해야하는 구간에서 차선을 변경하기 위해 side occupancy detect를 연구했었다.
([이전 게시글](https://dgist-artiv.github.io/vision/2020/11/16/Side_Occupancy_Check.html))
그런데 연구하는데 고민해야할 점이 두 가지 있었다.

## 차선 변경에서 어려운 점
1．vision에서 주는 길은 차선을 변경하는 주행유도선은 제공되지 않는다.

> tracking algorithm은 vision에서 주는 길을 그대로 따라간다.
> vision에서 차선 변경하는 경로를 임의로 그려줄 수 있는데,
> 차선 변경이 어느 정도 이루어졌는지 차의 위치를 정확히 확인을 할 수 없다. 
> 차선 변경이 완료되었다는 종료 조건을 고민해야 한다.

2．깜빡이를 키는 통신 프로토콜을 모른다.

> 깜빡이가 켜져있다는 상태만 can 통신을 통해 알 수 있다.
> 깜빡이를 키는 통신 프로토콜을 알 수 없어서
> 일단 최선의 방법으로 운전자가 깜빡이를 수동으로 키면 차선을 변경하도록 하였다.

## 차선 변경 종료 조건
vision 기반 tracking algorithm을 실행시키고 직접 차선 변경을 하면서 운전해본 결과, 생성되는 vision 주행유도선은 다음과 같은 경향을 보였다.
1. 차선을 변경하기 전 현재 차로의 주행유도선을 생성한다.
2. 목표 차로로 변경을 시작하면 현재 차로로 돌아가려는 주행유도선을 생성한다.
3. 차선을 넘어가면 목표 차로로 가려는 주행유도선을 생성한다.
4. 목표 차로에 완전히 넘어가면 목표 차로를 유지하려는 주행유도선을 생성한다. 이 주행유도선은 1번에서 생성한 주행유도선과 비슷해진다.

따라서 이러한 경향을 종료 조건에 적용시켰다.
종료 조건은 다음과 같다.
1. 깜빡이가 켜졌다는 신호가 들어오기 전의 안정적으로 주행한 현재 차로의 주행유도선을 저장한다.
2. 깜빡이가 켜지고 1번에서 저장한 주행유도선과 비교해 x축의 차이(threshold)가 크지 않으면 종료한다. 종료는 threshold가 20~30으로 설정했다.

|![lane_change_explanation](https://user-images.githubusercontent.com/53460541/113026298-e0236b80-91c3-11eb-95e1-5cd8b0329e5c.png)|
|:---:|
|차선변경시 생성하는 주행유도선의 경향과 종료 조건에 대하여 이해를 돕는 그림|

## 최종 결과
<iframe width="560" height="315" src="https://www.youtube.com/embed/dv-IxfmBnDc" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## To Do List
- 너무 급격하게 핸들을 조절해 승차감이 좋지 않다. --> 종료조건은 그대로 사용하되 가상의 주행유도선을 제공
- 저장한 주행유도선이 안정적으로 주행한 것이 맞는지 주행유도선에 대한 신뢰성을 검사해야 한다.
