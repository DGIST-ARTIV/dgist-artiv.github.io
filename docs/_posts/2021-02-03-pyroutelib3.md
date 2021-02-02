---
date: 2021-02-03
layout: post
title: Let's Use Pyroutelib3!
subtitle: pyroutelib3를 사용해보자
description: pyroutelib3를 사용해보자
image: https://user-images.githubusercontent.com/50894726/106676308-f5cc4880-65f9-11eb-9a5b-69308c54958e.png
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

Transport mode가 car인 경우, 아래와 같이 weights가 설정되어있다.

```
"weights": {
    "motorway": 10, "trunk": 10, "primary": 2, "secondary": 1.5, "tertiary": 1,
    "unclassified": 1, "residential": 0.7, "living_street": 0.5, "track": 0.5,
    "service": 0.5,
}
```

way의 tag에 각 키워드가 있으면 이를 파싱하여 weight를 설정하는데, 국토지리정보원에서 제공하는 정밀도로지도는 Openstreet Map 규격에 맞추어져 있지 않기 때문에 위와 같은 문제가 발생하는 것이다.

테크노폴리스 일대는 다 똑같은 일반도로이기 때문에, 굳이 weight을 다르게 설정할 필요가 없다.

Weight은 osmparsing.py 파일의 ```getWayWeight```에서 부여하는데, 이때의 return 값을 10으로 고정하였다.

![image1](https://user-images.githubusercontent.com/50894726/106675617-c406b200-65f8-11eb-8b31-df69fb61d075.png)

그러고 나서 재실행하면 작동은 하는데 딱히 결과값을 보여주지는 않는다.

단순 확인을 위해 시각화하는 부분까지 짜기는 귀찮으므로,, JOSM에 바로 import하여 눈으로 확인할 수 있도록 복사하기 쉬운 형태로 planning된 경로의 위도, 경도를 출력하도록 설정하였다.

최종 실행 코드는 아래와 같아졌다.

```
from pyroutelib3 import Router # Import the router
router = Router("car", "A2_LINK.osm") # Initialise it

start = router.findNode(35.7011876, 128.4585397)
end = router.findNode(35.6913484, 128.4639248)

status, route = router.doRoute(start, end) # Find the route - a list of OSM nodes

if status == 'success':
    routeLatLons = list(map(router.nodeLatLon, route)) # Get actual route coordinates
    for latlon in routeLatLons:
        print(str(latlon)[1:-1])
```

이제 실행해보면, 아래와 같이 주르륵 뜬다.

![image2](https://user-images.githubusercontent.com/50894726/106676276-e3520f00-65f9-11eb-9982-272caac9f5f1.png)

이걸 복사한 뒤, JOSM의 Lat Lon Tool을 이용하여 시각화하였다.

![image3](https://user-images.githubusercontent.com/50894726/106676308-f5cc4880-65f9-11eb-9a5b-69308c54958e.png)

이렇게 보면 얼추 잘된 것 같아보이지만...

![image4](https://user-images.githubusercontent.com/50894726/106676369-21e7c980-65fa-11eb-8656-4ac6cdf1a629.png)

가까이서 보면 역주행을 하고... 이상하게 주행하는 것을 확인할 수 있다.

#### 불완전한 Path Planning 원인 파악

그래서 위와 같이 결과가 나오는 원인을 파악해보았다.  
파악된 원인은 아래와 같다.

1. 역주행을 금지하지 않는다.
- 단순히 현재 노드와 연결된 다른 노드를 탐색하여 계산한다.
- 이를 해결하기 위해서는 way의 방향성을 무시하지 않도록(즉, way에 설정된 방향으로만 진행하도록) 수정해야한다.

2. way로 node간 연결이 되어있지 않은 경우, 차선변경을 하지 못한다.
- 올바른 주행 경로를 형성하기 위해서는 차선변경을 할 수 있어야한다. 그러나 node와 node사이가 way로 직접 연결되어 있지 않으면 이동할 수 없는 곳으로 간주한다.
- 따라서 차선 변경이 가능한 경우, 차선 변경을 위한 way를 만들어주어야한다.

#### TODO

위 문제를 해결하기 위해서 아래와 같은 작업을 진행할 예정이다.

- way의 방향성을 고려하여, 이전 node는 진행가능한 node로 간주하지 않도록 한다.
- 주행유도선 맵 파일과 차선 맵 파일을 이용하여, 차선변경이 가능한 구간의 경우 차선 변경을 위한 새로운 way를 형성하는 별도의 코드를 짠다.



(작성중)
