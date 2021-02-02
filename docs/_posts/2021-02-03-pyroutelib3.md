---
date: 2021-02-03
layout: post
title: Let's Use Pyroutelib3!
subtitle: pyroutelib3를 사용해보자
description: pyroutelib3를 사용해보자
image: 
category: hdmap
tags:
  - HD_Map
  - Path_Planning
  - osm
author: junsang
---
HD Map 정보를 publishing 해주는 패키지를 구상할때, 원래는 global path planning까지 구현할 예정이였다.

그러나 1차년도 계획에서는 지정된 셔틀 경로만 주행하기로 결정되며, global path planning은 하지 않았다.

몇 가지 공개된 툴을 이용하여 path planning을 시도하였으나, 실제 자율주행에 바로 적용하기에는 주행 방향을 고려하지 않는 문제 등으로 인하여 개발이 중단되었었다.

이번에 기존의 HD Map package의 문제점을 보완한 새로운 package 개발을 준비하며, path planing까지 구현하고자 한다.

몇 가지의 툴을 이용해본 결과, 우리 HD map package와 호환성이 높으면서도 개발하기 용이하다고 판단된 **pyroutelib3**를 사용하기로 결정하였다.

pyroutelib3를 사용해보고, 추가 개발이 필요한 부분을 정리하였다.

### [pyroutelib3](https://github.com/MKuranowski/pyroutelib3)

pyroutelib3는 [pyroutelib2](https://github.com/gaulinmp/pyroutelib2)를 기반으로 만들어졌다.

pyroutelib2는 온라인 상의 Openstreet Map을 이용하여 경로를 생성하는데, pyroutelib3는 로컬 osm 파일을 이용할 수 있다.

우리 상황에 맞게 이용하기 위하여, 코드를 약간 수정하여 테스트하였다.

기본 실행 코드는 아래와 같다.

```
from pyroutelib3 import Router # Import the router
router = Router("<transport mode>", "<path-to-.osm-file>")

start = router.findNode(lat, lon) # Find start and end nodes
end = router.findNode(lat, lon)

status, route = router.doRoute(start, end) # Find the route - a list of OSM nodes

if status == 'success':
    routeLatLons = list(map(router.nodeLatLon, route)) # Get actual route coordinates
```

```"<transport mode>"```는 ```"car"```로 설정하였고, ```"<path-to-.osm-file>"```은 테크노폴리스 일대의 주행유도선 파일로 설정하였다.

출발점과 도착점은 임의의 두 지점을 찍어서 위도와 경도를 입력하였다.

이렇게 단순히 실행을 하면 ```KeyError("findNode in an empty space")```가 발생한다.

(작성중)
