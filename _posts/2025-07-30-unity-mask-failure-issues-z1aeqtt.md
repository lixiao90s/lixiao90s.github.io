---
title: Unity Mask遮罩失效问题
date: '2025-07-30 14:34:27'
permalink: /post/unity-mask-failure-issues-z1aeqtt.html
tags:
  - unity
  - UGUI
layout: post
published: true
---





实际效果

![image](/assets/images/image-20250730143814-cygxc0l.png)

期望效果

![image](/assets/images/image-20250730143635-4r24aw6.png)

‍

## 环境设置

- Canvas设置![image](/assets/images/image-20250730144216-latiynl.png)

​​

- Camera设置![image](/assets/images/image-20250730144316-kwiuxap.png)

在当前情况下就会出现MASK失效的问题

在把相机设置成Screen Space - Overlay或者设置清除深度 如下图即可解决

![image](/assets/images/image-20250730144512-i5hzrsf.png)

我是设置了清除深度缓存，让其显示效果正常

## 相机渲染模式的区别

- ### Screen Space - Overlay

  - UI直接渲染在屏幕上，不依赖相机//

  - 不受3D场景影响//

  - 性能较好//
  -  遮罩效果稳定
  - 不需要深度测试
- ### Screen Space - Camera

  - UI渲染在指定相机前方的平面上

  - 受相机设置影响（FOV、深度等）

  - 可以与3D场景交互
  - 遮罩效果可能受相机设置影响
  - 需要正确的深度缓冲区管理

## Clear Depth 的作用原理

```
问题场景：
 - UI相机和游戏相机共享深度缓冲区
 - 游戏相机已经渲染了3D场景，设置了深度值
 - UI相机渲染时，深度测试可能失败
 - 导致UI元素（包括遮罩）不显示或显示异常
渲染流程：
 1. 游戏相机渲染3D场景 → 设置深度缓冲区
 2. UI相机渲染UI → 需要清除之前的深度值
 3. 如果不清除深度，UI可能被深度测试过滤掉
```
