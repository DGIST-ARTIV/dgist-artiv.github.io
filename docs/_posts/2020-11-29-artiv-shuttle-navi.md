---
date: 2020-11-29
layout: post
title: ARTIV_SHUTTLE_NAVI for HD Map Information Publishing
subtitle: artiv_shuttle_navi에 대해 알아보자.
description: artiv_shuttle_navi에 대해 알아보자.
image: https://user-images.githubusercontent.com/50894726/107152599-130f6700-69ac-11eb-8ac9-538479862f32.png
tags:
  - HD_Map
  - ROS_package
  - artiv_shuttle_navi
author: junsang
---
우리 연구팀은 [국토지리정보원에서 제공하는 정밀도로지도를 기반](https://dgist-artiv.github.io/hdmap/2020/05/10/shp2osm-josm.html)으로, [부족한 부분은 직접 채운 HD Map](https://dgist-artiv.github.io/hdmap/2020/05/16/draw-hdmap.html)을 사용하고 있다.

그러나 HD Map만 있다고 해서 이 정보를 그대로 자율주행에 사용할 수는 없다.

그래서 지도상의 데이터를 ROS topic으로 publish해주는 패키지가 필요하였고, 이를 위해 ARTIV_Shuttle_Navi를 개발하였다.

### Dependency

**1. Ubuntu 18.04 & ROS2 - Dashing**
- Artiv_Shuttle_Navi는 Ubuntu 18.04 버전에서 테스트 되었습니다.
- ROS2-Dashing 버전을 사용하며, python3로 개발되었습니다.

**2. Python Packages**
- [utm](https://pypi.org/project/utm/) ```pip3 install utm```
- [osmium](https://pypi.org/project/osmium/) ```pip3 install osmium```
- [shapely](https://pypi.org/project/Shapely/) ```pip3 install shapely```

**3. Subscribed Topic**
- gps_fix ([sensor_msgs/NavSatFix](http://docs.ros.org/en/api/sensor_msgs/html/msg/NavSatFix.html))
- 현재 위치를 파악하기 위해 NavSatFix 타입의 gps 데이터를 subscribe합니다.

**4. Map Data**
- ```~/ARTIV_Mapdata/osm_DGIST/``` 폴더에 Map file들을 넣어야합니다.
- 아래의 요소별 파일명 규칙에 따라야합니다.
	- 주행유도선 : A2_LINK.osm
	- 미션구역 : A4_MISSIONSECTION.osm
	- 선 형태의 도로 표지(정지선 등) : B2_SURFACELINEMARK.osm
	- 면 형태의 도로 표지(횡단보도 등) : B3_SURFACEMARK.osm
	- 과속방지턱 : C4_SPEEDBUMP.osm
- 파일명은 새로 추가한 A4_MISSIONSECTION.osm을 제외하면, [국토지리정보원의 규칙](http://map.ngii.go.kr/download/ms/pblictn/preciseRoadMap/%EC%A0%95%EB%B0%80%EB%8F%84%EB%A1%9C%EC%A7%80%EB%8F%84%20%EC%A0%9C%EA%B3%B5%20%EC%95%88%EB%82%B4.pdf)을 그대로 따릅니다.

### How it works

**1. 주행할 경로를 설정합니다.**
- 주행유도선 osm 파일상의 way ID가 나열된 형태로 입력.
- e.g. ```self.paths = [-143024, -142744, -142784, -142962, -143228, -143263, -142967]```

**2. 각 way에 담긴 node들을 하나씩 탐색하며 사전 설정한 도로 요소(정지선, 횡단보도, 과속방지턱 등)에 들어가는 부분이 있는지 확인합니다.**
- ![iamge1](https://user-images.githubusercontent.com/50894726/107152599-130f6700-69ac-11eb-8ac9-538479862f32.png)
- 만약 어떤 node들이 사전 설정 도로 요소 polygon 안에 들어가있다면, 처음 포함된 노드의 인덱스와 마지막 노드의 인덱스를 저장합니다. (polygon에 포함된 node가 한 개라면 동일한 인덱스가 저장됩니다.)
- 위 그림에서는 4개의 node가 polygon에 포함되어 있습니다.

**3. polygon 앞, 뒤에 있는 node들을 찾습니다.**
- 정보 전달 범위(e.g. 요소 20m 앞에서 부터 정보 전달)는 사전에 설정되어 있습니다.
- 2번 과정에서 찾은 node 인덱스를 이용하여, polygon에 포함된 node보다 앞에 있는 node를 탐색합니다.
- 그 node와 polygon 사이의 거리가 사전 설정 거리보다 멀어질때까지 탐색합니다.
- 사전 설정 거리 범위 안에 들어있는 node 중 가장 앞의 인덱스를 저장합니다.
- 마찬가지로, 2번 과정에서 찾은 node 인덱스를 이용하여, polygon에 포함된 node보다 뒤에 있는 node를 탐색합니다.
- 그 node와 polygon 사이의 거리가 사전 설정 거리보다 멀어질때까지 탐색하고, 범위 안의 node 중 가장 뒤의 인덱스를 저장합니다.

**4. 각 node에 정보를 저장합니다.**
- 2 ~ 3 과정에서 찾은 정보를 node에 저장합니다.
- 어떤 요소인지, 얼마나 떨어져있는지를 저장합니다.
- 그 요소를 향해 다가가고 있을 경우, 거리를 양수로 저장하고 그 요소와 멀어지고 있을 경우, 거리를 음수로 저장합니다.
- polygon에 포함된 경우는 0을 저장합니다.

**번외. 2 ~ 3 과정 예시**
	- ![image2](https://user-images.githubusercontent.com/50894726/107153294-0db41b80-69b0-11eb-9d8b-edaa1107a120.png)
- 위와 같은 상황이라고 가정해봅시다. (사전 설정 거리 20m)
- 과속방지턱 polygon에 포함된 3번 node가 찾아지고, 인덱스 3이 저장됩니다.
- 2번 node와 polygon 사이의 거리를 탐색합니다. 20m보다 가까우므로 넘어갑니다.
- 1번 node와 polygon 사이의 거리를 탐색합니다. 20m보다 가까우므로 넘어갑니다.
- 0번 node와 polygon 사이의 거리를 탐색합니다. 20m보다 멀기때문에 탐색을 멈추고, 인덱스 1이 저장됩니다.
- 4번 node와 polygon 사이의 거리를 탐색합니다. 20m보다 가까우므로 넘어갑니다.
- 5번 node와 polygon 사이의 거리를 탐색합니다. 20m보다 가까우므로 넘어갑니다.
- 6번 node와 polygon 사이의 거리를 탐색합니다. 20m보다 멀기때문에 탐색을 멈추고, 인덱스 5가 저장됩니다.
- 전체 노드들의 집합을 ```nodes```라고 하면, 아래와 같이 정리됩니다.
- ```
  speedbump_approach_nodes = nodes[1:3]
  speedbump_include_nodes = nodes[3]
  speedbump_leaving_nodes = nodes[4:6]
  ```
- 위 node범위에 맞추어 정보를 저장합니다.
- e.g.) 과속방지턱까지 13m 남은 경우, **+13**
- e.g.) 과속방지턱을 지난지 6.43m인 경우, **-6.43**

5. 모든 탐색을 마치면, 정보가 저장된 node들의 리스트를 반환합니다.

6. 현재 위치와 가장 가까운 node를 찾고, 그 node에 포함된 정보를 publish합니다.
- 차량 주행 시, gps를 통해 현재 위치정보를 파악합니다.
- 가장 가까운 node에 저장된 정보를 사전에 약속한 형태로 publish합니다.

### Published Topics

- /hdmap/path ([sensor_msgs/JointState](http://docs.ros.org/en/melodic/api/sensor_msgs/html/msg/JointState.html))
	- 앞으로 주행해야할 path를 utm 좌표로 publish합니다.
	- publish할 노드의 개수는 사전에 설정합니다. (본 연구팀은 30개로 사용, 대략 30m)
	- /hdmap/path/position
		- 앞으로 가야할 node의 utm x좌표
	- /hdmap/path/velocity
		- 앞으로 가야할 node의 utm y좌표

- /hdmap/node_info ([std_msgs/Float32MultiArray](http://docs.ros.org/en/melodic/api/std_msgs/html/msg/Float32MultiArray.html))
	- 현재 위치와 가장 가까운 node상에 저장된 정보를 publish합니다.
	- 기본 형태는 ```[0.0, 0.0, 0.0] * 정보 개수```로, 길이는 3의 배수입니다.
	- 각 인덱스가 담고 있는 정보는 아래와 같습니다.
		- 첫번째 : 요소 종류 (e.g. 횡단보도 : 1.0, 과속방지턱 : 6.0 등)
		- 두번째 : 요소 subID - 같은 요소가 여러개 존재할 경우를 대비하여, 각 요소 종류별로 번호가 부여됩니다. (e.g. 1번 횡단보도, 2번 횡단보도 등)
		- 세번째 : 거리정보 - 요소와의 거리를 담고 있습니다. (e.g. 12.4m 앞 : +12.4, within : 0, 3.23m 지나침 : -3.23)

- /hdmap/mission_info ([std_msgs/Float32MultiArray](http://docs.ros.org/en/melodic/api/std_msgs/html/msg/Float32MultiArray.html))
	- 작동원리나 형태는 ```node_info```와 동일합니다.
	- 다만 미션 정보를 별도로 전달하기 위해 구분하였습니다.
		- DGIST 원내 셔틀 주행의 경우, 정차 가능 구역 등

### Usage

- ```ros2 launch``` 명령어로 쉽게 실행 가능. ```ros2 launch artiv_shuttle_navi artiv_shuttle_navi.launch.py```
- 작동 영상 (클릭시 Youtube로 이동)

[![Video1](http://img.youtube.com/vi/nOwks9bHo1Y/0.jpg)](https://youtu.be/nOwks9bHo1Y)


### Comments
- ```/hdmap/path```에서 JointState msg를 사용한 것은 일종의 꼼수다. utm x좌표와 utm y좌표를 동시에 전달하기 위해, FloatMultiArray 2개를 동시에 전달할 필요가 있었는데 JointState msg가 3개(position, velocity, effort)의 MultiArray를 묶어서 보낼 수 있어서 선택하였다.

- 현재 버전은 우리 연구팀 상황에 맞춰진 경우에만 사용할 수 있다. 범용성을 확대하기 위해 구조를 어느정도 변경할 계획을 갖고 있다.

- 수정 뒤 오픈소스로 공개할 예정이다.
