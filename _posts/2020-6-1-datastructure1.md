---
title: 稀疏数组与队列
author: 晓
date: 2020-6-1 17:36:00 +0800
categories: [DataStructure]
tags: [稀疏数组 , 队列]
--- 

当一个数组中大部分元素是0，或者相同的值，可以使用稀疏数组来保存

处理方法：记录数组一共有几行几列，有多少个不同的值
把具有不同值得元素的行列及值记录在一个小规模数组中（稀疏数组），从而缩小程序的规模.

![稀疏数组]({{ "/assets/img/sample/data_structure1.png" | relative_url }}) 
