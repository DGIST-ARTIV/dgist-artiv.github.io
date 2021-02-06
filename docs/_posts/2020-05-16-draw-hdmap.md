---
date: 2020-05-16
layout: post
title: Let's Draw a Map!
subtitle: HD Map의 빠진부분을 채워보자
description: HD Map의 빠진부분을 채워보자
image: https://user-images.githubusercontent.com/50894726/107123561-7e403700-68e1-11eb-83a8-9c03bc127dca.png
category: hdmap
tags:
  - HD_Map
  - mapping
  - RTK_GNSS
author: junsang
---
DGIST가 위치해있는 테크노폴리스일대는 다행히도(?) 국토지리정보원에서 구축한 정밀도로지도가 제공된다.

![image1](https://user-images.githubusercontent.com/50894726/106588553-427a3a00-658e-11eb-8949-02383a857c4b.png)

그런데, DGIST 내부의 일부도로는 구축되어있지 않아, 구축할 필요가 있다고 생각하고 바로 테스트 작업에 착수하였다.

![image2](https://user-images.githubusercontent.com/50894726/107123511-4f29c580-68e1-11eb-83c5-afa89655bd8c.png)

### 차선 데이터 수집(테스트)

처음에는 차량을 이용하여 RTK-GNSS와 노트북에 전원을 공급하고, GPS 안테나를 차선에 유지시키는 방식으로 작업하였다.

차량은 저속으로 주행하고, 사람이(..) 차선에 맞추어 안테나를 움직여주었다.

[![Video1](http://img.youtube.com/vi/_nAcYMgrH9Q/0.jpg)](https://youtu.be/_nAcYMgrH9Q)

(클릭 시 Youtube로 이동)

이 방식으로 몇 시간 해보다가 너무 힘들어서 방법을 바꿨다.

![image3](https://user-images.githubusercontent.com/50894726/107116920-ec710380-68b9-11eb-84e2-b5b2af2fe495.jpg)

내 전동킥보드를 소환하여 보조배터리로 전원을 공급하고, 노트북은 들고다녔다.

[![Video2](http://img.youtube.com/vi/fbrslCNBGLo/0.jpg)](https://youtu.be/fbrslCNBGLo)

(클릭 시 Youtube로 이동)

바퀴로 밀고다니면 되어서 훨씬 수월하였다.

![image4](https://user-images.githubusercontent.com/50894726/107116922-fabf1f80-68b9-11eb-8cda-c88a2ec183a2.jpg)

ros nmea 드라이버를 이용하여 gps 데이터를 rosbag으로 기록하고, 추후 python을 이용하여 osm 데이터로 변환해주었다.

그런데 이렇게 수집한 데이터는 결국 이용하지는 않았다.

그 이유는 이전에 [RTK-GNSS Test & Setting [Part 1]](https://dgist-artiv.github.io/hdmap/2020/04/30/RTK-test.html)에서 밝힌 이유와 동일한데, GPS fixed를 확인하지 않아서 부정확한 데이터라고 판단하였기 때문이다.


### 최종 사용 방안

HD map상의 차선 데이터는 거의 활용하지 않고, 주행유도선을 주로 활용하게 되었다.

따라서 차량에 GPS를 부착한 뒤에, 셔틀 경로로 여러바퀴 주행하여, 가장 적합한 주행유도선을 제작하였다.

그렇게 만들어진 주행유도선 데이터는 아래와 같다.

![image5](https://user-images.githubusercontent.com/50894726/107123561-7e403700-68e1-11eb-83a8-9c03bc127dca.png)

ros nmea driver에서 제공하는 데이터를 이용하여, osm 파일을 제작하였다.

자동으로 지도 파일을 만들어주는 툴을 제작하였는데, 이와 관련해서는 약간의 수정을 거치고 추후 블로그 글을 작성할 예정이다.
