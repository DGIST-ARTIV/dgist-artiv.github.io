---
date: 2020-11-20
layout: post
title: Let's Make Side Occupancy Camera!
subtitle: 광각 카메라를 만들어보자
description: 광각 카메라를 만들어보자
image: https://user-images.githubusercontent.com/50894726/103971370-b7d03600-51ad-11eb-8ead-4b83864055a5.jpg
category: hwcomms
tags:
  - autonomous
  - hardware
  - vision
  - camera
author: junsang
---
차량의 차선변경 기능을 구현하기 위해서는, 측후방에 차량 혹은 장애물이 있는지를 판단할 수 있어야한다.

기존에 차선 및 장애물 인식용 카메라는 전방만 주시하고 있어서, 차량의 양 옆에 새로운 카메라를 장착하기로 결정하였다.

다만, 일반적인 웹캠을 설치하면 화각이 좁아 하나의 카메라로 측방과 후방을 감지하기가 매우 어렵고, 광각을 지원하는 카메라를 새로 구매하기에는 예산이 부족하다.

따라서 우리가 생각해낸 방법은 스마트폰용 광각렌즈를 활용한 마개조이다!

![image1](https://user-images.githubusercontent.com/50894726/103971597-670d0d00-51ae-11eb-8261-26bef7020ea1.png)

우리는 로지텍의 C920웹캠을 베이스로 광각 카메라를 제작하였다.  
너무 신난 상태에서 작업을 바로 들어가버려서 사진을 남기는 것을 깜빡했다..

![image2](https://user-images.githubusercontent.com/50894726/103971682-9459bb00-51ae-11eb-838d-5d18e0a53ae2.png)

광각 렌즈를 부착하기 위해서 렌즈 겉면에 장착된 보호 플라스틱을 제거해야한다.  
딱봐도 플라스틱 부분의 테두리에 양면테이프로 접착이 되어 있는 것 같아서, 히트건을 들고 살살 녹여주었다.

이후, 칼로 끝 부분을 조심스럽게 들어올려서 보호 플라스틱을 제거하였다.

![image3](https://user-images.githubusercontent.com/50894726/103971807-d682fc80-51ae-11eb-9227-3df06cc8cfac.jpg)

우선 손으로 광각렌즈를 잡고 usb를 연결하여 화각이 적절하게 형성되었는지 확인하였다.

위치를 잡은 후에는 보는 것처럼 글루건으로 살짝 고정을 실시하였다.

![image4](https://user-images.githubusercontent.com/50894726/103971916-25c92d00-51af-11eb-9512-55d4fcb33edd.jpg)

이런식으로 글루건 쏘기, 말리기를 반복하며 최대한 떨어지지 않도록 고정하였다.  

![image5](https://user-images.githubusercontent.com/50894726/103971370-b7d03600-51ad-11eb-8ead-4b83864055a5.jpg)

방수는 최선을 다하고, 기도를 통해 해결하였다(?)

![image6](https://user-images.githubusercontent.com/59161083/99421650-f54cdb80-2941-11eb-9b3c-71db246c64b9.jpg)

이런식으로 2개를 제작하여 차량의 양 옆에 부착하였다.

기술적인 부분은 이구 연구원이 작성한 [글(클릭 시 이동)](https://dgist-artiv.github.io/vision/2020/11/16/Side_Occupancy_Check.html)에서 확인할 수 있다.

