---
date: 2020-09-24
layout: post
title: Cruise Control based on pid [Part 1]
subtitle: PID 제어를 이용한 항속 주행 장치 개발
description: PID 제어를 이용하여 평지에서 일정 속도를 유지하도록 하는 cruise control 개발과정
image: https://user-images.githubusercontent.com/25432456/91651414-542f5100-eac7-11ea-8c2c-66eb21f90418.gif
optimized_image: https://user-images.githubusercontent.com/25432456/91651414-542f5100-eac7-11ea-8c2c-66eb21f90418.gif
category: control
tags:
  - Cruise Control
  - PID Control
  - Anti-windup
  - Control Algorithm
author: seunggi
---

# PID 제어를 이용한 항속 주행 장치 개발 Part 1. 

## 항속주행이 필요한 이유
Cruise control의 가장 큰 장점은 속도를 제어할 수 있다는 점이다. 기본적으로 자동차가 자율주행을 하기 위해서는 차량의 속도를 일정하게 유지하는 것이 중요하기 때문에 자율주행의 가장 기본이 되는 기능 중 하나이다.
이렇게 속도를 일정하게 유지하기 위해서는 단순 속도 제어가 아닌 Accel과 Brake actuator를 직접 제어해야 한다.

## 그렇다면 어떻게 항속주행을 구현할 것인가?
항속주행을 위해서는 가장 먼저 PID 제어에 대해 알아야한다. PID 제어란 피드백 제어기의 형태를 가지고 있으면, 제어하고자 하는 대상의 output 
