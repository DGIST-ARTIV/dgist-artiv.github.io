---
date: 2020-12-29
layout: post
title: ARTIV FSD Shuttle
subtitle: 완전 자율주행 교내 셔틀 서비스 런칭
description: 완전 자율주행 교내 셔틀 서비스 런칭
image: /assets/img/fsd_preview.gif 
category: blog
tags:
  - autonomous
  - shuttle
  - dgist
author: gwanjun
---

안녕하세요 자율주행연구팀 기술책임 신관준입니다.    
2020년 11월 말에 운전자 개입이 필요없는 완전 자율주행 서비스를 런칭하고 교내 몇몇 분들을 대상으로 베타 서비스를 런칭하였는데요
셔틀 자율주행은 실제로 공도로 나가는 자율주행보다 난이도가 낮고, 훨씬 저속이라 어떤 면에서는 난이도가 상대적으로 낮을 수 있으나, 개발 자체는 공도 및 골목길에서 발생할 수 있는 상황을 염두에 두고 개발하였고 오히려 교내에서는 무단횡단이나 중간에 정차된 차량들이 
많아서 예상했던 것보다 주행 정책에서 많은 시간을 소모했습니다. 실제로 성공한 날짜는 훨씬 전이긴 하지만 연말이라 보고서나 인수인계 절차로 팀 내부가 어수선해서 드디어 외부로 공개합니다.


물론 우리 블로그가 기술블로그니, 다른 연구실과 시연 영상과 다르게 모니터 스크린 화면도 따로 첨부했습니다. __사실 여기 들어오는 분들은 핸들 돌아가는 것보다 모니터 내용이 더 궁금한 거 압니다.__

### 원내 자율주행 External View
<iframe width="560" height="315" src="https://www.youtube.com/embed/a7RJmi-d-v4" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### 원내 자율주행 OnScreen View
<iframe width="560" height="315" src="https://www.youtube.com/embed/zy5BGoSApIk" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

On Screen View는 무선 화면 송수신 장치로 후방 차량에서 녹화했는데, 영상 품질이 좋지 않은점 양해부탁드립니다. 그래도 잘 나오는 부분이 훨씬 많습니다.

셔틀 주행을 위해서 주변 상황 인지로직이나, 주행 절차, 각종 알고리즘의 프로세스 스케줄링을 포함한 ARTIV Framework는 오픈소스로 공개할 예정입니다. 쉽게 이식이나 개조, 변경, 본인 랩에 맞출 수 있도록 간단한 구조를 자랑하는 우리 프레임워크는   
기능이나, 성능 면에서는 자율주행 기능이나, 후에 포스트로 올라올 고속 주행 ADAS 시스템에 대해서 높은 주행 성능과 안정성을 보장합니다. 몇몇 핵심 네트워크의 Weights 파일이나 데이터셋운 내부 회의에 따라 공개여부라 달라질 수 있으나... 우선 논문이 나와봐야 알겠네요.  

![img](/assets/img/framework_stack.png)
ARTIV 프레임워크는 Ubuntu 18.04 기반의 PC에서 ROS1과 ROS2를 병행 사용하는데요, 두 미들웨어에서 제공하는 장점이나 호환되는 언어에 최대한 호환성을 높일려고 노력했습니다. 그래서 이전 글에는 ros bridge에서 각종 Topic의 임베디드 PC에게는 아까운 자원을 꽤나 많이 사용해서 Selective Bridge라고 자체 솔루션도 만들어서 사용하고 있는데요
그리고 차선 인식과 객체 인식 딥러닝 같은 큰 GPU 로드가 필요한 Task의 경우에는 별로로 2개의 PC를 10GiGE 으로 연결하여 사용하는 것을 추천드립니다. 이 것도 ROS2 덕분에 간단한게 이용할 수 있도록 프레임워크에서 지원하고 있습니다.

하여간 약 8개월간 자율주행 통합 시스템 구축을 위한 연구를 진행했습니다. 코로나로 인해서 장비 수급이나 절차가 많이 늦어져서 그냥 시간을 보낸 달도 있었네요
우리팀은 이 프레임워크가
* 연구/교육 목적에서 쉽게 사용되었으면 합니다. ('자율주행을 한다' 것 보다는 더 깊숙하게 센서나 정책에 대한 연구가 이루어져야한다고 생각합니다.)
* 어떤 ROS 버전에서도 사용할 수 있도록 버전을 타는 ROS 기능은 사용하지 않았습니다. 놀랍게도 직접 구현하거나 별도의 프로그램을 따로 만들어놨습니다.   


기본적으로 이 프레임워크가 공개된다면 
* 미리 제공되는 GPS 기반의 맵을 기준으로 셔틀 주행을 할 수 있는 기능 (VISION + GPS Hybrid System)
* 차선 인식 기반 차선 조향 시스템 (급곡선, 고속 주행 대응)
* ADAS 시스템
* 편향 주행 및 자동 회피용 MPTG Planner
* LIDAR+Camera SensorFusion 기반의 객체 인지 알고리즘
* 주변 환경 인지 딥러닝 알고리즘 (Side Occpancy, Pedestrian Safety)

을 기본적으로 제공하는 구성으로 공개할 생각입니다.

<iframe width="560" height="315" src="https://www.youtube.com/embed/eDKE3Xn6om8" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

요즘은 학교 근처에 있는 KIAPI에서 고속 주행 테스트랑, EuroNCap의 자율차 테스트를 진행하고 있는데요 만족스러운 결과가 나와서 저희 로직에 대한 안정성이나 안전성에 대해서는 문제가 없도록 설계하는 중이죠  
전체 프레임워크는 ISO26262 표준을 따라서 설계는 했는데 이 표준은 정식 인증기관이 없어서 뭐 당당하게 말씀드릴 수는 없네요  



블로그가 듬성듬성 올라오는 터에 재밌게 보셨는지는 모르겠네요..ㅎㅎ.  
팀원들한테 많이 쓰라고 장려해도 전부 글쓰는 것보다 코드 작성하는게 좋은 친구들이라
조회수보면 많은 분들이 찾아와주시는데 후에 저희 레포가 공개되면 연구에 많은 도움이 되었으면 합니다.

감사합니다.


