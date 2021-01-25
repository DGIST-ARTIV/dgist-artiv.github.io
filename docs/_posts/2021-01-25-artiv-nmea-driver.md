---
date: 2021-01-25
layout: post
title: ARTIV_NMEA_DRIVER, GNSS Driver for ARTIV FrameWork
subtitle: ARTIV Framework를 위한 NMEA driver
description: ARTIV Framework를 위한 NMEA driver
image: https://user-images.githubusercontent.com/50894726/105720717-172c9500-5f67-11eb-8dcf-4c4389a86fe7.png
category: hdmap
tags:
  - autonomous
  - RTK-GNSS
  - GPS
  - driver
  - localization
author: junsang
---
(썸네일용 사진입니다. 아래에 다시 써놨습니다 ㅎㅎ..)

## About [ARTIV_NMEA_DRIVER](https://github.com/js-ryu/for_ARTIV_Framework/tree/main/artiv_nmea_driver)

artiv_nmea_driver는 ARTIV Framework에 사용하기 위한 NMEA 드라이버이다.

GNSS 장비로부터 NMEA신호를 받고, 이를 파싱하여 ROS로 Publish한다.

본 드라이버는 [nmea_navsat_driver](http://wiki.ros.org/nmea_navsat_driver)를 수정하여 제작되었고, 원 저자의 정보는 아래와 같다.

- Maintainer: Ed Venator <evenator AT gmail DOT com>
- Author: Eric Perko <eric AT ericperko DOT com>, Steven Martin
- License: BSD
- Source: git [https://github.com/ros-drivers/nmea_navsat_driver.git](https://github.com/ros-drivers/nmea_navsat_driver.git) (branch: melodic-devel)

현재 최신 artiv_nmea_driver 1.7.1은 [nmea_navsat_driver melodic-devel](https://github.com/ros-drivers/nmea_navsat_driver/tree/melodic-devel) 0.5.2를 기반으로 하고 있다.

### Sample Usage

1. Move the downloaded ```artiv_nmea_driver``` folder to ```~/catkin_ws(or another catkin workspace)/src```.

2. Open a terminal and go to ``~/catkin_ws/src(or your path)/artiv_nmea_driver/scripts`` .

3. Give permission using ```chmod +x nmea_serial_driver.py```.

4. Run ROS1, ```catkin_make```, and source!

5. Executing using the launch file.  
```$ roslaunch artiv_nmea_driver nmea_serial_driver.launch```

Default values for various parameters are specified in the ```nmea_serial_driver.launch``` file. By default your GPS is connected to ```/dev/artivGPS```(using symlink), and is communicating at 115200 baud.
  
### Published Topics

발행되는 토픽은 총 7개로, 기존 nmea_navsat_driver에 비해 4개가 많아졌다.

- gps_fix ([sensor_msgs/NavSatFix](http://docs.ros.org/en/api/sensor_msgs/html/msg/NavSatFix.html))
  - GPS position fix reported by the device. This will be published with whatever positional and status data was available even if the device doesn't have a valid fix. Invalid fields may contain NaNs.
  - 기존 드라이버의 ```/fix```와 동일하다.

- gps_vel ([geometry_msgs/TwistStamped](http://docs.ros.org/en/api/geometry_msgs/html/msg/TwistStamped.html))
  - Velocity output from the GPS device. Only published when the device outputs valid velocity information. The driver does not calculate the velocity based on only position fixes. The unit is m/s.
  - 기존 드라이버의 ```/vel```과 동일하다.

- gps_spd ([std_msgs/Float64](http://docs.ros.org/en/melodic/api/std_msgs/html/msg/Float64.html))
  - Speed output from the GPS device. Only published when the device outputs valid speed information. The unit is km/h.
  - ```gps_vel```은 속도이므로, 방향정보가 포함되어 있다. 우리는 방향정보가 포함되지 않은 단순 속력이 필요하였기에 추가하였다.

- gps_deg ([std_msgs/Float64](http://docs.ros.org/en/melodic/api/std_msgs/html/msg/Float64.html))
  - Expressed the vehicle's heading angle. The unit is deg, and the range is 0° to 360°. 0° is north, 90° is east, 180° is south, and 270° is west.
  - NMEA 데이터를 통해 차량의 heading angle을 파싱할 수 있다. 위에 작성한 것처럼 0도를 북쪽으로 하여 시계방향으로 회전하며 각도가 증가하느 형태이다.
  - 
  ![image1](https://user-images.githubusercontent.com/50894726/105725921-d8014280-5f6c-11eb-95c8-60c1cf3efa86.png)

- gps_yaw ([std_msgs/Float64](http://docs.ros.org/en/melodic/api/std_msgs/html/msg/Float64.html))
  - The heading angle of the vehicle is expressed in a different form than ```gps_deg```(for path tracking part). The unit is deg, and the range is -180° to 180°. 0° is east, 90° is north, -90° is south, and 180° or -180° are west. The upper part is positive and the lower part is negative based on the +x axis.
  - Path Tracking을 담당하는 파트에서 ```/gps_deg```와느 다른 좌표계로 형성된 yaw 데이터를 요구하여 추가하였다.
  - 
  ![image2](https://user-images.githubusercontent.com/50894726/105725929-db94c980-5f6c-11eb-98a5-ddf57a287681.png)

- utm_fix ([geometry_msgs/PoseStamped](http://docs.ros.org/en/melodic/api/geometry_msgs/html/msg/PoseStamped.html))
  - Present location in UTM coordinate system. The UTM zone is omitted and only the x and y coordinates are expressed.
  - 위도, 경도 좌표가 아닌 UTM 좌표가 필요하여 추가하였다.  
    
- time_reference ([sensor_msgs/TimeReference](http://docs.ros.org/en/melodic/api/sensor_msgs/html/msg/TimeReference.html))
  - The timestamp from the GPS device is used as the time_ref.
  
### Error Types

ARTIV Framework의 경고장치 작동조건에 맞추어 아래의 상황일 때 에러를 발생한다.

- Type 1(Fatal) : Invaild Checksum, Device Connection Fail
- Type 2(Error) : Value Error, HDOP exceeds 3, RTK is not Fixed

### Other Informations

ARTIV Organization은 private이라 팀원이 아닌 사람은 접근할 수 없기에, 개인계정에 repo를 생성하였다.
아래의 링크에서 다운로드 받을 수 있다.

- git : [https://github.com/js-ryu/for_ARTIV_Framework/tree/main/artiv_nmea_driver](https://github.com/js-ryu/for_ARTIV_Framework/tree/main/artiv_nmea_driver)
- Release : [https://github.com/js-ryu/for_ARTIV_Framework/releases/tag/1.7.1](https://github.com/js-ryu/for_ARTIV_Framework/releases/tag/1.7.1)

---------------------------------------------------------------------------------------------

#### Open Source Information
Software License Agreement (BSD License)

Copyright (c) 2013, Eric Perko  
All rights reserved

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

- Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.
- Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.
- Neither the names of the authors nor the names of their affiliated organizations may be used to endorse or promote products derived from this software without specific prior written permission.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
