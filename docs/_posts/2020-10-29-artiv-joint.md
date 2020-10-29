---
date: 2020-10-29
layout: post
title: ARTIV JOINT
subtitle: Mediating Node for Integrated Autonomous Driving, ARTIV JOINT
description: Mediating Node for Integrated Autonomous Driving, ARTIV JOINT
image: https://user-images.githubusercontent.com/59792475/97580902-42bae480-1a37-11eb-8672-4262a32d075a.png
optimized_image: https://user-images.githubusercontent.com/59792475/97580902-42bae480-1a37-11eb-8672-4262a32d075a.png
category: integratedsw
tags:
  - autonomous
  - software integration
  - PID gain scheduling
  - move car
  - ARTIV JOINT
author: juho
---

> DGIST 캠퍼스내 통제된 구역안에서 자율주행모드로 진행하였습니다.


# Lane Keeping Assist의 DGIST 원내 주행

## 하드웨어 준비
최근에 아이오닉의 센서 배치 때문에 하드웨어 구성이 조금 달라졌다.   
그에 따라서 카메라의 위치 및 각도도 바뀌게 되었다.   
카메라는 이전보다 아이오닉을 기준으로 아래에, 그리고 뒤로 옮기게 되었다.   
원래 이랬던 카메라가~   
<p align="center"><img src="https://github.com/DGIST-ARTIV/dgist-artiv.github.io/blob/master/docs/media/camera_old.jpg" width="50%" height="50%"></img></p>
아래처럼 바뀌었다.   
<p align="center"><img src="https://github.com/DGIST-ARTIV/dgist-artiv.github.io/blob/master/docs/media/camera_new.jpg" width="50%" height="50%"></img></p>   
더 조촐해지고 색도 특이(?)해졌다.(애들이 욕하더라...ㅠㅠ)    
하지만 이것은 단지 테스트를 위한 것일 뿐,    
위치가 확정된다면 더욱 깔끔하고 방수도 잘 되는 지지대를 만들 예정이다!   

# 소프트웨어 준비
차선 유지 시스템을 작동시키기 위해서는 약 9개의 커맨트 창을 실행시켜야 한다...   
특히 roscore나 bridge가 잠시 먹통이 되면 전부 껐다가 켜야 하는 참사가 발생...   
그런데 ros1 프로그램이나 ros2 프로그램이 먹통이 되면 잠시 껐다 키면 되니 걱정 하지말자^^   

다른 설정도 중요하지만 특히 중요한 것은 Affine 변환이다.   
위에서 본 것처럼 카메라의 위치가 달라졌기 때문에 Affine 변환 또한 변하게 된다.   
이 부분은 은빈이가 잘 하니까 걱정하지 않아도 된다 ㅎㅎ....   

# 본격적인 테스트
하드웨어 및 소프트웨어 작업을 마치고 본격적인 테스트를 해보자.   
우리가 테스트한 조건은 저녁이며(맨날 저녁에만 개발하니까...) 비 또는 눈이 오지 않는 날이다.   
매우 한정적인 조건이지만 그래도 괜찮은 결과가 나왔다.   
실제로 카메라의 위치가 바뀌어 걱정이 되었지만 생각보다 너무 잘돼서 깜짝 놀랐다.   
일단 사진을 클릭해서 영상을 확인해보자.   
<iframe width="560" height="315" src="https://www.youtube.com/embed/jk8l4TOWPlY" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
위 영상이 차 안을 전부 볼 수 있는 것은 아니지만 운전자는 오직 속도만 조절하고 있다.   
운전자가 오른손을 핸들에서 떼고 있으면 차선 유지가 작동되고 있는 것이다.   
차선이 없는 곳에서는 핸들이 떨리거나 갑자기 돌아가는 모습을 볼 수 있는데,   
이것은 GPS와 합쳐서 해결해나갈 일이다. (승기 파이팅!)   

# 문제점
보통 셔틀 운행은 해가 떠있는 낮에 활발하다.   
하지만 개발 및 테스트를 저녁에만 해왔었기에 우리 프로그램은 저녁에 적합한 것이었다.   
실제로 낮에 테스트를 해보았을 때, 핸들이 떨리는 구간이 많았으며   
오히려 저녁에 잘 안 되던 구간에서 잘 되는 경우도 확인됐다.   
낮과 저녁에서 각 프로그램의 성능을 확인하면서 개선할 필요가 있다.
카메라에서 영상의 프레임 수의 차이라던지, 각 프로그램들의 연산 속도가 그 예이다.
