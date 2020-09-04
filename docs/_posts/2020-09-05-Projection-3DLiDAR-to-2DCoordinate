---
date: 2020-09-05
layout: post
title: projection 3D LiDAR points to 2D coordinate 
subtitle: 라이다 3D 점을 2D로 사영
description: 라이다 3D 점을 2D로 사영
image: https://user-images.githubusercontent.com/42258047/91830447-c0ec4c00-ec7d-11ea-95e2-8440282e33d2.gif
category: sensorfusion
tags:
  - camera
  - lidar
  - sensorfusion
author: hyunjin
---


# projection the LIDAR points to 2D points in real-time 
Author : 배현진 <br/>
 > reference: https://github.com/reinforcementdriving/lidar_projection

## Environment Setting
OS: Ubuntu 18.04
opencv: 4.3.0   
ROS: melodic
language : c++

## Usage
3D인 라이다 점들을 2D 위에서 확인 할 수 있다. 

### how to calculation

reference site : (https://github.com/reinforcementdriving/lidar_projection) 에서 show.py를 들어가서 보면, 라이다의 3D coordinate를 2D로 projection하는 계산 식이 다음과 같이 있다. 여기서 우리는 적절한 h_res, v_res를 설정해서 projection된 이미지를 얻을 수 있다. 

'''    
    x_lidar = points[:, 0]
    y_lidar = points[:, 1]
    z_lidar = points[:, 2]
    r_lidar = points[:, 3] # Reflectance
    # Distance relative to origin when looked from top
    d_lidar = np.sqrt(x_lidar ** 2 + y_lidar ** 2)
    # Absolute distance relative to origin
    # d_lidar = np.sqrt(x_lidar ** 2 + y_lidar ** 2, z_lidar ** 2)
    v_fov_total = -v_fov[0] + v_fov[1]
    # Convert to Radians
    v_res_rad = v_res * (np.pi/180)
    h_res_rad = h_res * (np.pi/180)
    # PROJECT INTO IMAGE COORDINATES
    x_img = np.arctan2(-y_lidar, x_lidar)/ h_res_rad
    y_img = np.arctan2(z_lidar, d_lidar)/ v_res_rad   '''
    
    
### Excution Result

lidar_to_2d_front_view의 결과는 다음과 같다.
(다음 결과는 h_res = 0.4, v_res = 0.8 로 설정한 결과이다.)
https://user-images.githubusercontent.com/42258047/91830447-c0ec4c00-ec7d-11ea-95e2-8440282e33d2.gif


코드 중에 bird eye view로 전환해주는 코드를 이용한 결과는 다음과 같다. 
https://user-images.githubusercontent.com/42258047/92269604-7984ea80-ef1f-11ea-99a5-fe7e53e4d70a.png

