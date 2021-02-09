---
date: 2021-02-09
layout: post
title: ARTIV_OSM_FORMATTER [PART 2]
subtitle: artiv_osm_formatter의 모든것
description: artiv_osm_formatter의 모든것
image: https://user-images.githubusercontent.com/50894726/107383696-9a87e200-6b34-11eb-912c-a12184315dfd.png
category: hdmap
tags:
  - HD_Map
  - osm
author: junsang
---
[ARTIV_OSM_FORMATTER [PART 1]](https://dgist-artiv.github.io/hdmap/2021/02/06/artiv-osm-formatter-part1.html)에서 주행유도선을 구성하는 node 간격을 세분화 및 균일화하는 것에 대해 설명하였다.

위 글과 [Let's Use Pyroutelib3!](https://dgist-artiv.github.io/hdmap/2021/02/03/pyroutelib3.html)에서 개발이 필요하다고 말한 차로변경 way 형성 기능을 이번에 추가하였다.

### 차로변경 Way가 만들어지는 과정

**1. 주행유도선 파일과 차선 파일을 불러온다.**

우리 HD Map은 국토지리정보원의 것을 기반으로 하기 때문에, way에 작성되어 있는 tag 또한 그대로 이용하였다.

차선 정보가 들어있는 ```B2_SURFACELINEMARK.osm``` 파일을 보면, ```'Type'```이라는 tag로 차선 형태를 구별한다.

차로 변경이 가능한 경우는 백색 점선, 백색 좌점혼선(겹선의 형태로, 왼쪽은 점선 오른쪽은 실선), 백색 우점혼선(겹선의 형태로, 왼쪽은 실선 오른쪽은 점섬)인 경우이다.  
편도 1차로 도로에서 중앙선을 넘어 추월을 허용하는 황색 점선 등은 포함하지 않았다.

태그에 대한 자세한 정보는 [국토지리정보원의 문서](http://map.ngii.go.kr/download/ms/pblictn/preciseRoadMap/%EC%A0%95%EB%B0%80%EB%8F%84%EB%A1%9C%EC%A7%80%EB%8F%84%20%EC%A0%9C%EA%B3%B5%20%EC%95%88%EB%82%B4.pdf)를 통해 확인할 수 있다.

차선이 백색 점선인 경우에는 왼쪽에서 오른쪽, 오른쪽에서 왼쪽 모두 차선 변경이 가능하지만, 좌점혼선이나 우점혼선의 경우는 한쪽 방향으로만 차선 변경이 가능하다.

**2. 주행유도선 파일과 차선 파일을 파싱한다.**

위에서 말한 3가지 타입의 차로 변경 가능 구역을 별도의 list로 만들었다.
```
left_change = [] #왼쪽으로만 차선 변경이 가능할 경우 right to left
right_change = [] #오른쪽으로만 차선 변경이 가능할 경우 left to right
both_change = [] #양쪽으로 차선 변경이 가능할 경우 
```
이 list에는 ```'L_LINKID'```와 ```'R_LINKID'``` 정보가 tuple 형태로 들어간다.

```'L_LINKID'```는 차선 왼쪽에 위치한 주행유도선의 ID를 나타내는 tag이고, ```'R_LINKID'```는 차선 오른쪽에 위치한 주행유도선의 ID를 나타내는 tag이다.

각 LINKID를 통해 주행유도선 정보를 얻을 수 있는 것이다.

**3. 정한 규정에 맞추어 차로 변경 구간을 계산한다.**

- **최소 차로 변경 거리 규정**

	차로 변경을 너무 짧은 거리안에서 이루어지게 만들면 차량 주행시 안전에 문제가 있을 것이다.
	<br />
	물론 global path planning 단에서 반환되는 경로를 그대로 따라 주행하는 것은 아니고, 다른 센서들이 상황을 고려하여 주행하겠지만, 미리 이런 부분도 반영해놓는 것이 좋다고 생각하여 최소 차로 변경 거리를 정하였다.
	<br />
	차량에 GPS를 부착하고 실제 차로 변경을 여러번 해보고, 차로 변경시 필요한 거리가 어느정도인지 측정해보았다.
	<br />
	![image1](https://user-images.githubusercontent.com/50894726/107380279-14b66780-6b31-11eb-90fd-823671a7a851.png)

	(테스트를 위해 실제 주행한 경로)
	<br />
	내가 운전할때는 차로 변경시 최대한 여유롭게 천천히 움직이는 스타일이라, 차로 변경 할때 대략 40m를 움직이는 경향을 보였다.  
	너무 여유를 두지 않고 빠르게 차로를 변경할때는 20m 정도였다.
	<br />
	따라서 최소 차로 변경 길이는 20m로 정하였고(물론 사용자가 수정할 수 있다.), 실제 거리로 판단하기에는 코드 실행시간이 길어질 것을 우려하여, 노드 간격을 기준으로 20개의 노드가 떨어져야만 way가 형성되도록 구성하였다.
	<br />
	![image2](https://user-images.githubusercontent.com/50894726/107381888-a672a480-6b32-11eb-95ec-fad012c40eed.png)
	<br />
	위 그림처럼 0번 인덱스가 차로 변경 시작 지점이라면, 완료 지점은 20번 인덱스가 되는 것이다.
	<br />
	차로 변경 거리 말고도 한 가지의 조건이 더 있다.

- **way의 시작점에서는 차로 변경 금지**

	주행유도선의 way는 주행 경로 노드(A1_NODE)에 의해 구분된다.

	주행 경로 노드는 정지선, 진/출입시점, 회전 발생 시점, 터널, 교량 등 도로 속성에 어떤 변화가 생길때 만들어져 있는데, 이런 지점에서 차선 변경을 시도하는 것은 불확실한 위험성이 존재한다고 생각하였다.

	따라서 way가 시작하는 시점에서는 차로 변경을 하지 못하도록, 차로 변경 시작 시점을 10번 인덱스로 지정하였다.

	1m 간격으로 노드를 세분화하였을 경우, way가 시작한지 약 10m 지점에서부터 차로 변경을 허용한 것이다.

**4. 차로 변경 way를 형성한다.**

위 규칙들을 만족할 때 시점의 node ID와 종점의 node ID를 이용하여 새로운 way를 형성하였다.

이 way에 들어가는 tag는 아래와 같다.
```
{'Maker' : 'ARTIV_osm_formatter',
 'LinkType' : 'lane_change',
 'highway' : 'ARTIV_lane_change'}
```
어떤 용도로 어떻게 만들었는지 알기 위해 ```'Maker'```와 ```'LinkType'``` tag를 추가하였고, pyroutelib3를 이용하여 path planning을 수행할 때 차로 변경 way에는 가중치를 다르게 줄 수 있도록 ```'highway' : 'ARTIV_lane_change'```를 추가하였다.

차로 변경 way의 weight이 일반 주행유도선과 동일하여 불필요한 차로 변경을 유발하지 않게 하기 위함이다.

### Result

![image3](https://user-images.githubusercontent.com/50894726/107383696-9a87e200-6b34-11eb-912c-a12184315dfd.png)

ARTIV_osm_formatter를 실행시킨 최종 결과는 위와 같다.

왼쪽은 원본 ```A2_LINK.osm```파일이고, 오른쪽은 output파일이다.

노드가 촘촘해지고, 차로 변경 way가 추가되었음을 알 수 있다.

### USAGE

ARTIV_osm_formatter의 코드는 [github](https://github.com/js-ryu/for_ARTIV_Framework/tree/main/artiv_osm_formatter)에서 확인할 수 있다.

지난번엔  ```sys.argv```를 이용해 input, output 파일명을 지정하였는데, ```argparse```를 이용하는 것으로 변경하였다.

--file 혹은 -f 인자를 통해 ```<--input_link_file--> <--input_lane_file--> <--output__file_name-->```을 지정할 수 있고, --option 혹은 -o 인자를 통해 ```<--node_interval--> <--#_of_lane_change_nodes-->```을 지정할 수 있다.

기본값은 아래와 같다.
```
LINK File : "A2_LINK.osm"
LANE File : "B2_SURFACELINEMARK.osm"
Output File : "A2_LINK_output.osm"

Node Interval : 1 meter(s)
# of Lane Change Nodes : 20
```

- **사용 예시**
	- ```python3 main.py``` -> 기본값으로 실행
	- ```python3 main.py -f input.osm lane.osm``` -> LINK File : input.osm / LANE File : lane.osm / Output File : input_output.osm, 나머지는 기본값
	- ```python3 main.py --option 0.5``` -> Node Interval : 0.5미터, 나머지는 기본값
	- ```python3 main.py -f input.osm lane.osm test_out.osm -o 0.3 30``` -> LINK File : input.osm / LANE File : lane.osm / Output File : test_out.osm / Node Interval : 0.3미터, # of Lane Change Nodes : 30개

