---
date: 2020-02-16
layout: post
title: 자율주행 미들웨어 적용과 실적용 결과
subtitle: ROS1, ROS2 시스템 적용
description: ROS1, ROS2 시스템 적용
image: /assets/img/ros1_bridge_wallpaper.jpg
category: vision
tags:
  - ros
  - dashing
  - melodic
  - ros1_bridge
author: gwanjun
---

안녕하세요 자율주행 연구팀의 기술 책임을 맡은 신관준입니다. 처음 자율주행 시스템의 구성을 맡아서 고민이 많은데요, 그래서 개발의 편의를 위해서 ROS의 도입을 결정해서 팀원들한테 열심히 알려주고 있는데, 내부 세미나도 열고 핵심 기술이나 고민하는 사안들고 공유하고, 제작된 SW는 코드도 공유하고요

그래서 우리가 미들웨어를 선정하는 시점에서는 ROS1 Melodic, ROS2 Dashing을 두개 놓고 엄청 고민을 했습니다. 우리가 자율주행에 사용하는 주요 센서들은 ROS에 대응도 잘 되어있고, 참고할 수 있는 좋은 프로그램들이 많아서 미들웨어로 ROS를 사용하게 되었습니다.

TF나 Keras, Pytorch 프레임워크가 잘 돌아가는 Python3를 기본적으로 지원하는 ROS2로 마음이 쏠렸다가 주요 드라이버나, 커뮤니티가 ROS1으로 구성이 절대적으로 우세하여서 고민이 참 많았습니다.

데이터량으로 보았을 때, RealTime의 속성이나 대용량 데이터의 교환에 유리하다는 ROS2의 DDS, RTPS 를 고려하여도 차량 카메라, 라이다, 레이다, 내부 통신 데이터등 수많은 데이터들의 Inter process간 통신을 위해서 미들웨어를 사용하는 만큼 ROS2를 도입하는 것이 옳다고도 생각했습니다.

이 글을 작성하는 시점에는 ROS1 후속 판에서 Python3를 지원한다고 했는데, 저희가 연구 초기에 기준 사양을 정할 때에는 공개 및 자료도 별로 없어서 논외로 생각하겠습니다.

## ARTIV Framework의 ROS 버전별 개발 비율

그래서 그나마 타협안으로 Bridge를 사용해서 ROS1, ROS2를 모두 사용할 수 있게 진행하였습니다. 이는  각 파트원의 재량으로 원하는 언어로 개발하고, 오픈소스 및 ROS1이나, ROS2 편하게 개발하도록 했는데요 현재는 ROS1, ROS2의 개발 비중이 거의 비슷합니다.

|![rosVersion](/assets/img/statistic_rosVersion.png)|
|:--:|
|개발된 ROS1, ROS2 로직의 비율 <br> 지금까지 작성된 로직은 총 50개 놀랍게도 25,25로 양분하고 있다.|

Python으로 빠르게 로직이나 좋은 오폰소스 라이브러리로 빠르게 검증하고, 후에 속도를 위해서 Cpp로 다시 포팅하는 방식으로 진행하고 있습니다.


Vision 관련 로직들은 그래서 대부분 ROS2로 작성되어 있습니다.

파이썬으로 작성했던 코드들은 cpp로 합치면서 몇개의 py 파일을 한개의 cpp 플젝으로 넣어서 그렇지만 지금까지 개발한거 보면, **ros1 - cpp로 다시 회귀하고 있는 양상이 보이기 시작합니다.**

|![rosVersion](/assets/img/statistic_programmingLanguage.png)|
|:--:|
|몇몇 드라이버는 ros1-py2 기반으로 작성되어 있어서 꽤 존재한다.|

그 외로 디버깅용 툴도 ros1에서 사용하게 python2로 그냥 만들어서 사용하는 경우가 많아서 꽤 차지 한다.

그러면 디버깅 및 UI 를 관리하고 있는 통합SW부문을 제외한 드라이버, 주행, 제어, 인지, 판단의 핵심 노드들은 어떤 양상을 가지고 있을까요

|![rosVersion](/assets/img/statistic_rosplanning_langs.png)|
|:--:|
|cpp가 가장 큰 파이를 가지고 있다.|

다른 연구진들은 ROS도 안쓰는 곳도 있고, 모든 노드를 CPP로 다 처리하는 경우도 있는데, 저희는 꽤 가지각색으로 적용해서 사용합니다.

**어떤 언어든 사용할 수 있어서 정해진 처리 성능과 자원 소모 정도만 충족되면 사용할 수 있게 해둬서 이러한 양상이 나오는 듯 합니다.**

물론 대부분은 CPP로 포팅을 합니다.


## ROS1-ROS2 Bridge 사용의 문제점

우리가 작성한 프로그램의 성능은 좋아도, Bridge의 처리 능력이 생각보다 좋지 않다는 것을 여러 사례를 통해 알게되었는데요

Vision의 경우 ROS1의 기반의 FLIR 사의 SDK를 사용해서 이미지를 가져오는데, 처리는 ROS2에서 진행해서 Bridge가 대용량의 이미지만 들어오면 다른 패킷들도 당연하지만 지연이 발생하게 됩니다. 하지만 이런 지연이 발생하면 안되서 ROS2로 다시 드라이버를 작성해서 처리하게 되었는데요

이 떄부터 Bridge가 성능에 유리하도록 작성은 되지 않았구나를 느낍니다. 대부분 이러한 대용량 토픽들만 아니면 잘 넘겨주는데요, 동시에 사용하지 않는 토픽들고 Bridge가 무작정 받아서 변환하니 디버깅용으로 놓는 이미지 형태나 고대역폭, 높은 재생률을 가지고 있는 토픽들이 있으면 Bridge가 죽으면서 다른 로직들이 동작하지 않아서

ROS Bridge에서 사용할 수 있는 다른 방법을 찾아보기 시작합니다.
  - dynamic bridge
  - static bridge
  - parameter bridge

#### Dynamic Bridge
  가장 사용하기 쉽고, 공식 repo에서도 그냥 이거 사용하라고 제시하는 방법인데요, 저희는 필요한 토픽만을 전달해야되기 때문에 이 방식은 사용하면 안됩니다.


#### Static Bridge
  원하는 노드에서 라이브러리처럼 지정하는 노드를 bridge 할 수 있는 방식입니다. 헌데 코드 안에 ros1, ros2가 필요하고 make 주체도 ament_cmake, catkin_make가 공존해서 개발 환경 셋업이 난해하여 적용하지 않기로 하였습니다.

#### parameter Bridge
  별도의 프로그램으로 프로그램 시작시에 넘기고 싶은 토픽들의 타입과 토픽 이름을 rosparam의 형태로 전달해서 정해진 토픽만 전달하는 방식입니다. 저희가 추구하는 방식과 정확하게 일치하지만 심각한 문제가 있었습니다.
  parameter bridge 코드 안에는 놀랍게도 ros1, ros2의 자체 msg 타입을 정의하고 있지 않아서 ros1 에서 `std_msgs/Image` 라면 ros2에서는 `std_msgs/msg/Image`라서 경로가 서로 달라서 서로 다른 토픽이라 전달이 불가능하다고 오류가 뜨는 상황이 발생했습니다.

  이게 이상한건 dynamic bridge는 서로 상호 대응하는 msg를 정의해서 알아서 적용시켜서 보내는데, parameter bridge의 코드에는 그게 없습니다.

  ~~~ cpp
  // Dynamic Bridge 의 코드
  bool mapping_found = ros1_bridge::get_1to2_mapping(ros1_type_name, ros2_type_name);
  // 무려 mapping을 한다.
      if (!mapping_found) {
        // printf("No known mapping for ROS 1 type '%s'\n", ros1_type_name.c_str());
        continue;
      }
      // printf("topic name '%s' has ROS 2 publishers\n", topic_name.c_str());
    } else {
      ros2_type_name = ros2_subscriber->second;
      // printf("topic name '%s' has ROS 1 publishers and ROS 2 subscribers\n", topic_name.c_str());
    }

  ... //중략
  // 그렇게 받은 데이터를 각각의 Topic name을 넣어서 처리한다.

  bridge.bridge_handles = ros1_bridge::create_bridge_from_2_to_1(
  ros2_node, ros1_node,
  bridge.ros2_type_name, topic_name, 10,
  bridge.ros1_type_name, topic_name, 10);


~~~

~~~ cpp
//parameter bridge의 코드
//rosparam으로 topic, type, queue 만 받아놓는다.
for (size_t i = 0; i < static_cast<size_t>(topics.size()); ++i) {
  std::string topic_name = static_cast<std::string>(topics[i]["topic"]);
  std::string type_name = static_cast<std::string>(topics[i]["type"]);
  size_t queue_size = static_cast<int>(topics[i]["queue_size"]);
  if (!queue_size) {
    queue_size = 100;
  }
  printf(
    "Trying to create bidirectional bridge for topic '%s' "
    "with ROS 2 type '%s'\n",
    topic_name.c_str(), type_name.c_str());

  try {
    ros1_bridge::BridgeHandles handles = ros1_bridge::create_bidirectional_bridge(
      ros1_node, ros2_node, "", type_name, topic_name, queue_size);
      // 그렇게 받은 정보를 그냥 넣는다. mapping도 안한다.
    all_handles.push_back(handles);
  } catch (std::runtime_error & e) {
    fprintf(
      stderr,
      "failed to create bidirectional bridge for topic '%s' "
      "with ROS 2 type '%s': %s\n",
      topic_name.c_str(), type_name.c_str(), e.what());
~~~

핵심은 `ros1_bridge::create_bridge_from_2_to_1();` 와 `   ros1_bridge::create_bidirectional_bridge();`를 파라미터를 하나 더 받아서 앞의 함수로 처리만 해주면 원하는 native topic만 골라서 전송이 가능한데 왜 그거를 지원을 안할까

그래서 우리는 저렇게 만들어서 사용한다.

그랬더니 Bridge가 쾌적하고, 빠른 속도로 사용가능해진다. 많은 양의 데이터가 브릿지를 사용하고 있어서 브릿지의 처리가 늦는다면 ** 보내는 노드도 Publish 하는데 지연이 발생한다!**

Ros1_bridge는 저사양 작업에는 편리하나 메인스트림 데이터를 전송하는 것에는 정말 안좋은 방식이다. 필요한 경우 직접 UDP 로 localhost로 보내는 방식을 적용해봐도 좋을 듯 하다. 2개
bridge를 개조하든, udp 로 전송하고, 받는 방식을 만들든 오래 안걸리는데 왜 이렇게 고민을 했나 싶다.


(계속 작성중.)
