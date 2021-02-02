---
date: 2020-05-10
layout: post
title: Shp to Osm Conversion Using JOSM
subtitle: JOSM을 이용하여 Shp 파일을 Osm 파일로 변환해보자
description: JOSM을 이용하여 Shp 파일을 Osm 파일로 변환해보자
image: https://user-images.githubusercontent.com/50894726/106588553-427a3a00-658e-11eb-8949-02383a857c4b.png
category: hdmap
tags:
  - HD_Map
  - josm
  - conversion
author: junsang
---
연구에 사용할 HD map을 완전히 처음부터 구축하기에는 매우 어렵고, 시간상 불가능하였다.

다행히도 DGIST가 위치한 테크노폴리스 일대는 국토지리정보원에서 구축한 정밀도로지도가 제공된다.

[여기](http://map.ngii.go.kr/ms/pblictn/preciseRoadMap.do)에서 다운로드 받을 수 있다.

다만, 연구에 사용할 지도 포맷은 osm인데 제공하는 파일 형태는 shp이라 변환이 필요하였다.

그래서 JOSM을 이용하여 shp를 osm으로 변환하였다.

JOSM 설치법은 [여기](https://josm.openstreetmap.de/wiki/Download)에서 확인할 수 있다.

하지만 안타깝게도, JOSM은 기본적으로 shp 파일을 지원하지 않는다. 하지만 플러그인 설치를 통해 해결할 수 있다.

JOSM을 열고, Edit - Preferences로 들어간다.

![image1](https://user-images.githubusercontent.com/50894726/106590070-1364c800-6590-11eb-9dc8-2fc89281173c.png)

왼쪽에 있는 콘센트 모양이 플러그인이다.

opendata를 검색하고 설치하면된다. 

![image2](https://user-images.githubusercontent.com/50894726/106589922-ea443780-658f-11eb-9c29-3eb343a46ba6.png)

이젠 shp를 JOSM으로 열 수 있다!

![image3](https://user-images.githubusercontent.com/50894726/106588553-427a3a00-658e-11eb-8949-02383a857c4b.png)

잘 열렸다면 osm으로 변환하기 위해, File - Save As에서 osm으로 저장하면 끝이다.

![image4](https://user-images.githubusercontent.com/50894726/106590512-85d5a800-6590-11eb-9231-044a86669d25.png)
