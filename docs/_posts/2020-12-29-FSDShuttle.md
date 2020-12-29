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
기능이나, 성능 면에서는 자율주행 기능이나, 후에 포스트로 올라올 고속 주행 ADAS 시스템에 대해서 높은 주행 성능과 안정성을 보장합니다. 몇몇 핵심 네트워크의 Weights 파일이나 데이터셋운 내부 회의에 따라 공개여부라 달라질 수 있으나, 우선 논문이 나와봐야 알겠네요
블로그가 듬성듬성 올라오는 터에 재밌게 보셨는지는 모르겠네요, 팀원들한테 많이 쓰라고 장려해도 전부 글쓰는 것보다 코드 작성하는게 좋은 친구들이라
조회수보면 많은 분들이 찾아와주시는데 후에 저희 레포가 공개되면 연구에 많은 도움이 되었으면 합니다.

감사합니다.


