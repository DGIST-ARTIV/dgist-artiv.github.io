# ARTIV Blog guidline

1. 글 쓰는 방법.  
  ~잘 쓰면 된다~
  
  1. docs 폴더 안에서 _author 폴더에 이름 등록하기
  
 파일을 영문 이름.md (성 아님) 으로 만들고
  아래 내용을 md 파일 최상단에 붙여넣기
  ~~~
  ---
  layout: author
  photo: https://avatars1.githubusercontent.com/u/25432456?s=460&u=9072cd95bc308c7369c2eaaa6412684b3d5c7c0c&v=4
  name: gwanjun
  display_name: Gwanjun Shin
  position: Tech Leader
  bio: Just a developer.
  github_username: shinkansan
  ---

  ~~~
  
  name -> 저자id임 : 이름으로 하셈.   
  photo -> 프로필 이미지임, 온라인에서 따오는거 추천, github issue에 등록했다 빼도 되고, github 프로필에 등록한 이미지의 주소를 갖다 붙여도 됨.   
  display_name -> 이름 클릭하면 나오는 풀네임.  
  position -> 직위 (그냥 Researcher | 자기 파트 이름).  
  bio -> 한줄 자기 소개.  
  github_username -> 기트허브 닉네임.   


일단 이것만 만들면 다음부터는 _posts 폴더에서 글만 쓰면 된다.

파일 이름에는 규칙이 있다.
> 2020-08-29-fcw-test.md.   
처럼 년-월-일-제목-이후-에는-다시-를-많이-써도-됨.md

아쉽게도 이 시스템은 폴더별 정리를 아마 지원안할껄? 나도 모름.  

여튼 그렇게 만든 markdown 파일 최상단에 아래 내용을 붙여넣기

~~~
---
date: 2020-08-29 20:04:34
layout: post
title: Forward Collision Assist Test
subtitle: 전방 충돌 방지 보조 알고리즘 테스트
description: 전방 충돌 방지 보조 알고리즘 테스트
image: https://user-images.githubusercontent.com/25432456/91639157-9f0d8200-ea4f-11ea-8472-4ba8e84ead2e.jpeg
category: lidar
tags:
  - autonomous
  - deep_learning
  - lidar
author: gwanjun
---
~~~

위의 Author와 동일한 방식으로 적어줘야 함.  
여러분들이 꼭 작성해야하는 것은.  
date -> 작성일시.    
title --> 주제목.  
subtitle --> 부제목.  
description --> 그냥 부제랑 동일하게 하셈   
image --> 아무런 사진이 없는 글이라도 꼭 사진은 첨부하길 바람.   
category --> 이게 곧 팀별 분류가 됨, 팀 이름은 /docs/category 폴더의 md 파일명이다.   
tags --> 해시태그, 양심적으로 2개는 넣어주자.  
author --> 이거 꼭 넣어야함 자기 영어 이름을 넣자.  


그리고 이 '---' 아래에 원하는 내용을 적기 시작하면 됨

그 외는 일반 마크다운 문법과 동일하니 블로그 잘 써주세요~
