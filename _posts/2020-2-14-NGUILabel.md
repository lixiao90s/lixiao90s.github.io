---
layout: post
title: "NGUILabel走马灯"
categories: [NGUI]
tags: [NGUI]
author: LiXiao
---

* content
{:toc}

## 需求  

在项目接近尾声的时候，由于本地化的差异，导致很多ui超过了显示框，这时候运营方提出
需要在部分地方加上滚动，当文字超过显示的框的时候，自动滚动。由于项目已经接近尾声，所以务必
改动要小。在不改变UI结构，以及方便策划快速更改的情况下，开发的组件。     

## 实现

设置如图   

![设置]({{ "/assets/NGUItext.png" | relative_url }}) 

``` 
using System;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

/// <summary>
/// 文字走马灯效果
/// </summary>
[RequireComponent(typeof(UIPanel))]
[RequireComponent(typeof(UILabel))]
public class ScrollLabelNew : MonoBehaviour
{
   private UIPanel panel;
   private UILabel label;

   Vector2 panel_offset;
   private Vector3 start_pos;
   public float speedx;
   public float offset;
   float mtime;
   public float stop_time;
   
   private void Awake()
   {
      panel = GetComponent<UIPanel>();
      label = GetComponent<UILabel>();
      panel_offset = panel.clipOffset;
      start_pos = label.transform.localPosition;
   }

   private void OnEnable()
   {
      int max_depth = 0;
      foreach (UIPanel panel in transform.GetComponentsInParent<UIPanel>())
      {
         if (panel.depth > max_depth)
         {
            max_depth = panel.depth;
         }
      }
      panel.depth = max_depth+1;
   }

   private void Update()
   {
      if (panel == null || label == null)
      {
         return;
      }
      if (speedx < 0)
      {
         if (label.worldCorners[3].x < panel.worldCorners[3].x+offset)
         {
            if (mtime > 0)
            {
               mtime = mtime - Time.deltaTime;
               return;
            }

            mtime = stop_time;
            label.transform.localPosition = start_pos;
            panel.clipOffset = panel_offset;
         }
      }
      else
      {
         if (label.worldCorners[3].x > panel.worldCorners[3].x+offset)
         {
            if (mtime > 0)
            {
               mtime = mtime - Time.deltaTime;
               return;
            }

            mtime = stop_time;
            label.transform.localPosition = start_pos;
            panel.clipOffset = panel_offset;
         }
      }
      label.transform.localPosition += new Vector3(speedx * Time.deltaTime, 0, 0);
      panel.clipOffset+=new Vector2(-speedx * Time.deltaTime, 0);
   }
}    
```

## 效果    
![创建shader菜单]({{ "/assets/Feb-24-2020-21-10-04.gif" | relative_url }}) 
