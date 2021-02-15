---
date: 2021-02-15
layout: post
title: Let's Do Global Path Planning!
subtitle: pyroutelib3를 수정하여 global path planning을 해보자
description: pyroutelib3를 수정하여 global path planning을 해보자
image: image1](https://user-images.githubusercontent.com/50894726/107947124-4ddd5480-6fd5-11eb-9477-cb42ce4990fd.

![Screenshot from 2021-02-15 21-27-10](https://user-images.githubusercontent.com/50894726/107950051-897a1d80-6fd9-11eb-8b4b-fcbee028d6ea.png)
category: hdmap
tags:
  - HD_Map
  - Path_Planning
  - osm
author: junsang
---
지난번에 path planning을 하는데에 [pyroutelib3](https://github.com/MKuranowski/pyroutelib3)를 사용한다고 하였다.

[Let's Use Pyroutelib3!](https://dgist-artiv.github.io/hdmap/2021/02/03/pyroutelib3.html)에서 앞으로 해야할 일을 정리하였는데, 차로 변경을 위한 새로운 way를 형성하는 것은 [ARTIV_osm_formatter](https://dgist-artiv.github.io/hdmap/2021/02/09/artiv-osm-formatter-part2.html)를 이용하여 해결하였고, way의 방향성을 무시하여 역주행을 허용하는 것은 pyroutelib3를 수정하여 이번에 해결하였다.

### 차로 변경 way의 weight값 추가

[ARTIV_osm_formatter](https://dgist-artiv.github.io/hdmap/2021/02/09/artiv-osm-formatter-part2.html)에서 차로 변경 way에는 weight을 다르게 줄 수 있도록 tag에 'highway' : 'ARTIV_lane_change'를 추가했다고 하였다.

다양한 weight으로 테스트해보았을 때, 0.5일 경우가 불필요한 차로 변경이 가장 적어서 0.5로 설정하였다.

pyroutelib3의 ```types.py```의 car에 설정된 weights을 아래와 같이 수정하였다.

```
"weights": {
    "motorway": 10, "trunk": 10, "primary": 2, "secondary": 1.5, "tertiary": 1,
    "unclassified": 1, "residential": 0.7, "living_street": 0.5, "track": 0.5,
    "service": 0.5, "ARTIV_lane_change": 0.5,
}
```

### 역주행 방지

pyroutelib3에서 역주행 경로를 형성하는 이유는, way의 방향성을 무시하고 단순히 한 node에 연결된 다른 node들을 모두 갈 수 있는 node로 취급하기 때문이다.

그 이유는 모든 way가 양방향으로 주행할 수 있는 경로라고 생각하기 때문인데, 처음에는 ```datastore.py```에서 무조건 way의 방향성을 따라서만 cost를 저장하도록 수정하였다.

```
# Calculate cost of this edge
cost = self.distance(node1Pos, node2Pos) / weight
self.routing.setdefault(node1Id, {})[node2Id] = cost

'''
# Is the way traversible forward?
if oneway >= 0:
    self.routing.setdefault(node1Id, {})[node2Id] = cost
# Is the way traversible backwards?
if oneway <= 0:
    self.routing.setdefault(node2Id, {})[node1Id] = cost
'''
```

주석처리한 부분을 보면 알겠지만, 원래는 oneway의 여부에 따라 cost를 한쪽으로만 저장하거나 양방향 모두를 저장하는데 우리 HD map상에서의 way는 모두 단방향 주행만 가능하므로, way의 방향대로만 cost를 저장하도록 수정하였다.

그런데 원본 코드를 최대한 훼손하지 않는 선에서 이 문제를 해결하고 싶어서 찾아보던 중, ```osmparsing.py```에서 ```oneway``` tag에 따라 방향을 설정해준다는 것을 발견하였다.

```
def getWayOneway(way: dict, profile_name: str) -> int:
    """Checks in which direction can this way be traversed.
    For on-foot profiles only "oneway:foot" tags are checked.

    Returns:
    - -1 if way can only be traversed backwards,
    -  0 if way can be traversed both ways,
    -  1 if way can only be traversed forwards.
    """
    oneway = 0

    # on-foot special case
    if profile_name == "foot":
        oneway_val = way["tag"].get("oneway:foot", "")
        if oneway_val in {"yes", "true", "1"}:
            oneway = 1
        elif oneway_val in {"-1", "reverse"}:
            oneway = -1

        return oneway

    # Values used to determine if road is one-way
    oneway_val = way["tag"].get("oneway", "")
    highway_val = way["tag"].get("highway", "")
    junction_val = way["tag"].get("junction", "")

    # Motorways are one-way by default
    if highway_val in {"motorway", "motorway_link"}:
        oneway = 1

    # Roundabouts and circular junctions are one-way by default
    if junction_val in {"roundabout", "circular"}:
        oneway = 1

    # oneway tag present
    if "oneway" in way["tag"]:
        value = way["tag"]["oneway"]
        if value in {"yes", "true", "1"}:
            oneway = 1
        elif value in {"-1", "reverse"}:
            oneway = -1
        elif value in {"no"}:
            oneway = 0

    return oneway
```

그래서 그냥 ARTIV_osm_formatter에서 모든 way에 ```'oneway' == 'yes'```라는 tag를 추가하는 것으로 변경하였다.

이렇게 수정한 주행유도선 파일을 이용하여 global path planning을 수행하면 아래와 같이 경로가 만들어진다!

![image1](https://user-images.githubusercontent.com/50894726/107947124-4ddd5480-6fd5-11eb-9477-cb42ce4990fd.png)

가까이서 보아도 역주행을 하는 증상이 사라졌음을 알 수 있고, 불필요하게 차선변경을 많이하지도 않는다는 것을 볼 수 있다.

![image2](https://user-images.githubusercontent.com/50894726/107950051-897a1d80-6fd9-11eb-8b4b-fcbee028d6ea.png)

이렇게 수정한 pyroutelib3를 ARTIV_mission_navi에 적용하여 목적지를 설정하면 자동으로 주행 경로를 publish하도록 할 예정이다.
