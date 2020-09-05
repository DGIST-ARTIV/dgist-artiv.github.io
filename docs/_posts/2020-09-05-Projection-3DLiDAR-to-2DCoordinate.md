---
date: 2020-09-05
layout: post
title: projection 3D LiDAR points to 2D coordinate 
subtitle: 라이다 3D 점을 2D로 사영
description: 라이다 3D 점을 2D로 사영
image: https://user-images.githubusercontent.com/42258047/92272571-c0c1aa00-ef24-11ea-8191-91b3e5d26faa.gif
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

라이다 점은 (x,y,z)로 3 dimension을 가지는 좌표에 존재하는데 이를 2개의 축에 대한 점으로 바꿔서 생각하기 위해 다음과 같이 생각 할 수 있다. 

<img src="https://user-images.githubusercontent.com/42258047/92302759-982fc380-efa9-11ea-9b53-5d30a8385c5d.png" width="300" height="300" /> <img src="https://user-images.githubusercontent.com/42258047/92302761-9a921d80-efa9-11ea-8b43-4a82c98c94f0.png" width="300" height="300" /> <img src="https://user-images.githubusercontent.com/42258047/92302762-9c5be100-efa9-11ea-862e-50ee6a37c70a.png" width="300" height="300" />

reference site : (https://github.com/reinforcementdriving/lidar_projection) 에서도 show.py를 들어가서 보면, 라이다의 3D coordinate를 2D로 projection하는 계산 식이 다음과 같이 있다. 여기서 우리는 적절한 h_res, v_res를 설정해서 projection된 이미지를 얻을 수 있다. 

```  
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
    y_img = np.arctan2(z_lidar, d_lidar)/ v_res_rad   
```

    
### Excution Result

lidar_to_2d_front_view의 결과는 다음과 같다.
(다음 결과는 h_res = 0.4, v_res = 0.8 로 설정한 결과이다.)

![lidar_to_2d_front_view](https://user-images.githubusercontent.com/42258047/92270065-573f9c80-ef20-11ea-89a1-16f17d22810f.gif)


코드 중에 bird eye view로 전환해주는 코드를 이용한 결과는 다음과 같다. 

![bird_eye_view](https://user-images.githubusercontent.com/42258047/92269604-7984ea80-ef1f-11ea-99a5-fe7e53e4d70a.png)


### projection LiDAR points on image

![Screenshot from 2020-09-05 03-00-23](https://user-images.githubusercontent.com/42258047/92272188-0f227900-ef24-11ea-8385-6f86dd292876.png)

