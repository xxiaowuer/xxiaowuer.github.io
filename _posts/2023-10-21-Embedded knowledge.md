---
title: Embedded knowledge
date: 2023-10-21 14:36:36 +0800
categories: [EMBEDDED, Common sense]
tags: [Common sense]    # TAG names should always be **lowercase**
---

## 什么是TTL电平？

TTL标准认为在0~0.4V区间为逻辑0，在2.4V-5V区间为逻辑1

而stm32中最大电压为3.6V，所以在stm32中2.4-3.6V为逻辑1

TTL一般用于短距离通讯，因为其高低电平范围较小，容易受外部环境干扰，产生错误

## 串口校验

串口协议中为了避免外部环境的干扰，增加了串口校验，分为三种：

* 无校验
* 固定校验：在校验位固定为高电平或低电平，使用较少
* 奇偶校验：奇校验（保持一帧数据中高电平的个数为奇数个）；偶校验（保持一帧数据中高电平的个数为偶数个）