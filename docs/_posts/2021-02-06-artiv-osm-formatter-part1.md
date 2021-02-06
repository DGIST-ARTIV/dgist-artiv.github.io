---
date: 2021-02-06
layout: post
title: ARTIV_OSM_FORMATTER [PART 1]
subtitle: artiv_osm_formatter의 모든것
description: artiv_osm_formatter의 모든것
image: https://user-images.githubusercontent.com/50894726/107101166-cae02f80-6859-11eb-80a5-2825eb0e8ee1.png
category: hdmap
tags:
  - HD_Map
  - osm
author: junsang
---
현재 연구팀에서 사용하고 있는 HD map information publisher(ARTIV_SHUTTLE_NAVI)는 주행유도선을 구성하는 node에 도로 시설물(과속방지턱, 정지선, 횡단보도 등)정보를 사전에 담아놓은 뒤, node 데이터를 제공하는 형태이다.

![image1](https://user-images.githubusercontent.com/50894726/107099914-450eb500-6856-11eb-8249-e0771e001550.png)

그런데 국토지리정보원에서 제공하는 고정밀지도는 node 간격이 균일하지 않고, node 사이가 매우 멀리 떨어져있는 경우도 많다.  
따라서 원본 map의 node를 이용하여 node기반 도로 정보 전달 프로그램을 구성하는 것은 무리가 있다.

그래서 사전에 node간 거리를 거의 일정하게 조정해주고, 촘촘하게 만들어주는 작업이 필요하여 python으로 osm을 열고 수정하여 저장하는 툴을 개발하였다.

송주호 연구원이 만들어준 OSMHandler를 이용하여 기존 osm data를 불러들이고, haversine 공식으로 node간 거리를 계산하여 설정한 node간 거리(현재 사용중인 것은 1m)에 가장 가깝게 만들어 주는 툴이다.

사전에 한 번만 실행시키면 이후에는 실행시킬일이 없는 과정이기도 하고, 급박하게 개발하느라 소요시간은 크게 고려하지 않은 채 개발하였다. 그런데 이번에 global path planning을 개발하면서, 자율주행차량이 주행할 수 있는 범위를 늘리기 위해 지도 데이터의 범위를 확대시키니 osm 파일을 수정하는 데 소요되는 시간이 지나치게 길어졌다.

[Let's Use Pyroutelib3!](https://dgist-artiv.github.io/hdmap/2021/02/03/pyroutelib3.html)글의 TODO list에 '차선변경이 가능한 구간의 경우 차선 변경을 위한 새로운 way를 형성'할 필요가 있다고 적어두었다. 이 기능을 artiv_osm_formatter에 추가하는 것이 좋다고 생각하였고, 개발 이전에 기존 작동 범위에 대한 최적화가 필요하다는 생각이 들어 대폭 수정하였다.

### 수정된 내용

**1. numpy 도입**
- 기존에는 python의 list로 데이터를 다뤘는데, node id를 검색하는 등의 과정에서 시간이 꽤 많이 소요되었다. 이 문제를 해결하고자 numpy를 이용하여 데이터 구조를 수정하였다.

**2. 발생할 수 있는 오류 수정**
- 개발한 프로그램은 node를 추가로 만들어주는 기능을 한다. 그렇기 때문에 새로운 node에 새로운 ID를 부여해주어야 하는데, 기존 node의 id와 겹칠 수 있다는 것을 고려하지 않고 만들어져있었다. 중복 ID가 발생하지 않도록하는 과정을 추가하였다.

**3. Input, Output 파일명 지정가능**
- `sys.argv`를 도입하여 input, output 파일명을 지정할 수 있게 하였다. `main.py <--input_file_name--> <--output_file_name-->`의 구조로 입력하면 된다.
- **사용 예시**
	- `main.py` -> 기본값으로 실행(Input 파일 : "A2_LINK.osm", Output 파일 : "A2_LINK_output.osm")
	- `main.py map.osm` -> input 파일명 지정, output 파일 네이밍 자동(Input 파일 : "map.osm", Output 파일 : "map_output.osm")
	- `main.py map.osm last_map.osm` -> input, output 파일명 지정(Input 파일 : "map.osm", Output 파일 : "last_map.osm")

별로 한게 없어보이지만, numpy를 적용하는 데에 꽤 많은 시간이 걸렸다...

### 수정 결과

![image2](https://user-images.githubusercontent.com/50894726/107100858-e4cd4280-6858-11eb-99ac-dd0a363401a2.png)

툴이 실행되는데 걸리는 시간을 측정해보았다.  
실행시간이 대폭 감소되었음을 확인할 수 있다.

기존 툴의 경우 5분 이상 걸렸던 작업을 수정한 툴을 이용하면 1분 13초만에 마무리할 수 있다.

### BEFORE & AFTER

![image3](https://user-images.githubusercontent.com/50894726/107101166-cae02f80-6859-11eb-80a5-2825eb0e8ee1.png)

노란색 부분이 node인데, 한눈에 봐도 많아졌음을 확인할 수 있다.

![image4](https://user-images.githubusercontent.com/50894726/107101221-fcf19180-6859-11eb-9716-3a9dce8ade3f.png)

확대해서 보면 위와 같다.

