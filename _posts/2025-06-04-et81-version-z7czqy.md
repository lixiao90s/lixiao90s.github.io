---
title: ET8.1版本
date: '2025-06-04 08:47:07'
permalink: /post/et81-version-z7czqy.html
layout: post
published: true
---



# ET8.1版本

 ET8.1

- model和modelView 主要是放类的定义
- hotFix和hotFixView 主要放类的视线
- 带View的就是unity视图相关的
- Unity.loader下是非热更的函数

‍

## ECS 组件式编程

- 与unity官方的ECS毫无关系

ET 框架设计原则：

- 树状领域
- 组合模式
- 事件驱动
- 逻辑分发

后期编写全部在

- AllHotFix

  - System
- AllModel

  - component 和entity

- Model 逻辑层，不能引用任何unity下的代码
- ModelView 表现层，可以使用unity
- 客户端代码，必须在ET.Client命名空间当中，Server同理，如果是两者都用命名空间则是ET
- 实体和组件都会继承Entity
- 通过ComponentOf(typeof(Computer)) 来表明当前组件会挂在Computer上
- 表现层调用逻辑层是没问题，但是逻辑层应该通过抛出事件的方式，驱动逻辑层做出修改

  ‍

  ‍

  ### 为什么System要用static

‍

## Fiber

![7fa237e61827b28bd5ac8bbaa64d5575](http://127.0.0.1:61725/assets/7fa237e61827b28bd5ac8bbaa64d5575-20250625083528-0yajur7.jpg)

![6e1ad008fc15671f89d7ee6218418ba1](http://127.0.0.1:61725/assets/6e1ad008fc15671f89d7ee6218418ba1-20250625083820-onl8o85.jpg)

![734de6bcbb0176922bc33be5e95ad65f](http://127.0.0.1:61725/assets/734de6bcbb0176922bc33be5e95ad65f-20250625083905-udlorz6.jpg)

![4380a4e6f64083adb692901219f10574](http://127.0.0.1:61725/assets/4380a4e6f64083adb692901219f10574-20250625083959-eblv0yb.jpg)

![762b1f77e76f02662e708d1b555f63de](http://127.0.0.1:61725/assets/762b1f77e76f02662e708d1b555f63de-20250625084548-9eiw0is.jpg)

![031a3fda7242ae49a50ff668ca32332f](http://127.0.0.1:61725/assets/031a3fda7242ae49a50ff668ca32332f-20250625084909-9zcfykz.jpg)

![6db830b5ac7ebd1dd1c48fac1cde8ceb](http://127.0.0.1:61725/assets/6db830b5ac7ebd1dd1c48fac1cde8ceb-20250630084331-sm36igd.jpg)

![a587cdf64af7384c34eded940eddff7a](http://127.0.0.1:61725/assets/a587cdf64af7384c34eded940eddff7a-20250630085656-odo2fyy.jpg)

两个纤程通讯使用的ProcessInnerSender
