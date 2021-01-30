---
date: 2021-01-27
layout: post
title: Selfmade DTG(WIP)
subtitle: rosbag을 이용하여 차량의 주행정보를 분석하기!
description: 자율주행 임시운행 허가를 위한 DTG 만들기
image: https://user-images.githubusercontent.com/59161083/105984936-2b46d280-60de-11eb-9411-8a07a1ea9b79.png
category: vision
tags:
  - automation
  - rosbag analysis
  - rosbag analysis with Window
  - Data Analysis
author: gu
---
Author : 이  구 <br/>
Date: 2021.01.27

## Environment Setting
Bagpy must be installed.

```(python)
pip3 install bagpy
```

## Why?
우리는 현재, 지난 1년간 개발한 IONIQ을 이용하여 자율주행차량 임시운행허가를 받기위해 준비중이다.
이를 위한 필수조건 중 하나로, 주행기록계(DTG)를 설치하여야 한다. 그 근거는 아래와 같다.

- 자율주행자동차의 안전운행요건 및 시험운행 등에 관한 규정 제 17조 (운행기록장치 등)
   자율주행자동차에는「교통안전법」제55조제1항에 따른 운행기록장치를 장착하여야 하고, 운행기록장치 또는 별도의 기록장치에 다음 각 호의 항목을 저장하여야 한다.     
		 1. 자율주행시스템의 작동모드 확인   
		 2. 제동장치 및 가속제어장치의 조종장치 작동상태   
		 3. 조향핸들 각도   
		 4. 자동변속장치 조종레버의 위치   

이를 위해, IONIQ 차량에 운행기록계를 설치하기 위해 다양한 업체들에 연락한 끝에 설치할 수 있었지만, 위의 법령에 해당하는 모든 정보를 기록해주지는 않았다.
또, 이와 별개로, 자율주행임시운행 허가를 위한 제출서류 중 하나인, 사전시험주행보고서의 항목들에 해당하는 정보(Auto/Manual 모드 별 주행거리, 평균 속도 등)들 역시 얻을 수 있어야 했다. 
그래서, 우리의 자체 차량 ROS 통신 프로토콜 IONIQ INFO를 이용하여 위의 정보들을 직접 뽑아내기로 했다.

## How?
bagpy를 이용하면, Window 환경에서도 rosbag 파일을 쉽게 분석할 수 있다.
자세한 사용법은 [공식 docs](https://jmscslgroup.github.io/bagpy/)를 참고하자. 

코드를 짜기 전에, 어떤 정보를 얻어야 할지 미리 생각해보자.
법령에서 정한 네가지 항목은 각각 우리의 /Ioniq_info의 Auto Standby Switch, APS Feedback, BPS Feedback, Steering Angle에 대응한다.

bagpy의 기능 중 하나인 bagreader라는 class를 이용하면, 아래와 같이 손쉽게 각 topic에 해당되는 csv 파일을 얻을 수 있다.
```(python)
b = bagreader('[bag file name]')
b.message_by_topic('/topic name')   
```

이제, 다양한 정보들을 bagpy를 통해 손쉽게 얻어낼 수 있다는 것을 알았으니, 어떤 방식으로 구현해야할 지 생각해보자.

첫번째로, 평균 속도, 핸들 조향각, Auto Standby Switch, APS Feedback, BPS Feedback의 정보는 시간에 따른 값의 변화를 matplot을 이용하여 표현하기로 하였다.
이를 위해, csv파일에 함께 저장되어 있는 rostime을 시작점을 기준으로 하는 상대시간으로 바꿔주었다. 

두번째로, 그래프에 시각적인 정보를 추가하여 Auto(자율주행모드)/Manual(운전자우선모드) 정보를 쉽게 알 수 있도록 하였다.
이를 위해, matplotlib의 axvspan 기능을 이용하였다.
magenta 구간은 Auto(자율주행모드) 모드, cyon 구간은 Manual(운전자우선모드) 모드 이다.

세번째로, 각 값들의 시스템상 min/max 값을 빨간색 dashed line으로 표시해 주었다.


네번째로, 실제 주행시간을 알 수 있도록, rostime을 KST(Korea Standard Time)로 바꾼 후, 함께 표기해줄 수 있도록 하였다.
그 과정은 아래와같다.

```(python)
    def rostime2datetime(self, rostime):
        time2 = time.localtime(rostime)
        real_rostime = round(rostime) 
        y = time2.tm_year 
        m = time2.tm_mon
        d = time2.tm_mday
        t_h = time2.tm_hour
        t_m = time2.tm_min
        t_s = time2.tm_sec
        t_ms = str(round(rostime - real_rostime,4)).split(".")
        t_ms = t_ms[-1] 
        return str(y)+"-"+str(m)+"-"+str(d)+" "+str(t_h)+":"+str(t_m)+":"+str(t_s)+":"+str(t_ms)
```

다섯번째로, Auto Standby Switch의 값이 바뀌는 시점을 기준으로 각 section을 구분하였고, 각 section별 주행기록 정보와 전체 주행기록 정보를 분석하였다. 
얻을 수 있는 정보는 Auto/Manual 모드. 주행 시간, 평균 속도, 주행 거리 등이다.
txt 파일에 bag file 이름, 주행시작 시간(KST), 주행종료 시간(KST), total average speed, total auto driving time, total manaul driving time, total auto driving distance, total manual driving distance 등을 기록 한 후, 각 섹션별 Auto/Manual 모드, 주행 시간, 평균속도, 주행거리를 기록하였다.

여섯번째로, GPS 정보를 이용하여 차량의 위치를 기록하였고, 이를 이용하여 차량의 대략적인 이동경로를 알 수 있도록 하였다.

마지막으로, 한번의 실행으로 폴더안의 모든 bag file에 대한 분석이 이루어질 수 있도록 자동화 하였다.

## Result
위의 과정을 통해 얻은 결과는 아래와 같다.   

1. 각 값들에 대한 plot   

![2021-01-22-16-52-47_reason](https://user-images.githubusercontent.com/59161083/106358230-2be5a000-634e-11eb-879a-8dd32814673e.png)

2. 세부 주행 정보   

![Screenshot from 2021-01-30 22-55-41](https://user-images.githubusercontent.com/59161083/106358246-56375d80-634e-11eb-9a1f-8a296d403a77.png)

3. GPS를 통해 얻은 주행경로   

![2021-01-22-16-52-47_reason_gps](https://user-images.githubusercontent.com/59161083/106358269-81ba4800-634e-11eb-999f-872914191638.png)

