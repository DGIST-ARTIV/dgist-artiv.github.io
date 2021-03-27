---
date: 2021-02-25
layout: post
title: Lane Keeping System & Smart Cruise Control TEST (part 1, LKS)
subtitle: LKS and SCC test (part1)
description: LKS and SCC test (part1)
image: /assets/img/210225_LKA_SCC_test.gif
category: blog
tags:
  - vision
  - autonomous
  - LKS
  - SCC
  - dgist
author: eunbin
---

2021년 새해를 맞이하고 여전히 자율주행 연구를 이어갔고, 1월과 2월 두 달동안 임시운행 허가를 받기 위해서 노력을 기울였다.
자율주행 임시운행 허가를 위한 서류 중 사전시험주행 보고서를 작성하는 것이 필수였다.
우리는 시스템 최고속도를 100km/h로 설정을 했고 고속에 대한 테스트는 더 이상 학교에서 테스트할 수 없었다.
이를 위하여 학교에서 벗어나 학교 근처 KIAPI에서 두달간 사전시험주행을 진행했다.

![photo](/assets/img/kiapi_high-speed_circuit.PNG)

우리가 사용한 KIAPI의 주행로는 고속주회로로 R=100m의 뱅크부와 편도 3차선으로 이루어진 구간이다.
곡률은 학교의 셔틀 경로가 더 컸지만 이 주회로의 곡선도로는 학교에 비하면 정말 길다. (처음 테스트할 때는 언제 곡선을 벗어날 수 있는지 굉장히 힘들었던 기억이 있다.)
실제 운전석에서 bank부를 들어갈 때 이런 느낌으로 곡선의 끝이 보이지 않는다.
<p align="center"><img src="/assets/img/kiapi_bank_entrance.png" width="50%" height="40%"></img></p>

테스트 환경 설명은 이쯤에서 마무리하고 본격적으로 제목에서도 알 수 있듯이 LKS와 SCC 테스트에 대해서 이야기하고자 한다.

### LKS(차로 유지 시스템)
LKS는 차로 유지 시스템으로, Steering의 제어권을 갖고 있으며 차량의 속도에도 영향을 미치는 시스템이다.
차량의 속도보다는 Steering, 즉, 횡방향 제어에 큰 영향을 미치는 시스템이다.
임시운행 허가 조건만 봐서도 차로 유지 시스템은 항상 켜져 있는 상태로 시험을 보게 된다.
그만큼 중요한 시스템이고, 편향 주행, 차로 변경 외에는 Steering의 제어권은 이 시스템이 갖고 있기 때문에 driver에게 언제 제어를 맡길지 결정하는 것이 중요하다.
제어권에 대한 내용은 다음에 소개할 예정이다.

우리에게 이 시스템은 가장 먼저 ioniq에 탑재된 시스템으로 2학기 개강부터 종강, 겨울방학까지 6개월간 쭈욱 함께하여 오랫동안 마음을 쓴 시스템이기도 하다.
LKS의 변천사만 보면 다음과 같다.
1. vision 처리를 통한 차선 인식 방법 탐색 (차선 인식 딥러닝 네트워크 선정, 차선 데이터 가공 방법 등등)
2. vision 기반의 경로 제공 및 pure pursuit tracking algorithm에 적용
3. 데이터셋 augmentataion 및 pure pursuit 개조
4. HD map의 경로와 vision 경로를 혼용 [관련 글 보기](https://dgist-artiv.github.io/control/2020/09/13/vision-gps-integration-part2.html)
5. Stanley와 결합
6. 곡률에 따른 속도 조절

자동차 전용도로는 진입과 진출을 빼면 차선에 대한 혼동이 없기 때문에 vision 기반의 경로를 이용해도 문제가 없다는 판단으로
(물론, 더 안전한 시스템을 만들기 위해서는 차선책도 있어야하지만) 우리는 vision 기반의 경로를 이용하는 pure pursuit를 이용하여 임시운행 허가를 받기로 결정했다.
vision 기반의 경로를 생성하는 데 총 runtime이 30FPS 정도였고, 이 정도의 runtime을 가지고 있어도(더 빨랐으면 훨씬 좋았겠지만..) 직진 도로에서 120km/h까지는 안전하게 주행할 수 있었다.
차선을 인식하는 딥러닝 네트워크의 weight 파일은 augmentation 이미지 포함 약 60만장의 이미지가 학습된 weight 였다.

우리는 사전주행을 많이 하면 할수록 좋기 때문에 우리는 오전 10시부터 오후 7시까지 테스트를 진행했다.
2달이란 기간을 오랜 시간동안 테스트하다보니 시간에 따른 해의 위치나 밤운행, 주회로의 복잡도, 날씨 등 환경의 변화에 대한 테스트도 진행하게 되었다.ㅎㅎ
시스템에 대한 약간의 수정, 데이터 수집으로 변화에 대응하였고, 그 결과는 다음과 같다.

<iframe width="560" height="315" src="https://www.youtube.com/embed/5POMPtsQw7Y" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

이 날은 안개가 꽤 자욱이 껴있는 환경이었는데 차선 인식에는 큰 지장을 주지 않아서 잘 작동하는 모습이다. 차량의 속도는 80km/h이었다.

