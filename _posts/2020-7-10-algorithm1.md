---
title: 常用算法 一
author: 晓
date: 2020-7-10 14:16:00 +0800
categories: [数据结构与算法]
tags: [java]
---

## 常用算法

### 非递归二分查找实现

二分查找只适用于有序查找，需要先将数列进行排序

```java
package com.learn.bsc;

/**
 * 非递归二分查找
 */
public class BsNoRecur {

    public static int binarySearchNoRecur(int arr[],int target)
    {

        int left=0;
        int right = arr.length -1;
        while(left<=right)
        {
            int mid =(left+right)/2;
            if(arr[mid]==target)
            {
                return mid;
            }else if(arr[mid]>target)
            {
                right=mid-1;
            }else
            {
                left=mid+1;
            }
        }
        return -1;
    }
}

```

### 分治算法

分治算法可以求解的一些问题

- 二分搜索
- 大整数乘法
- 棋盘覆盖
- 合并排序
- 快速排序
- 线性时间选择
- 最接近点对问题
- 循环赛日程表
- 汉诺塔

#### 分治算法设计模式

![分治]({{ "/assets/img/sample/fenzhi.png" | relative_url }})

#### 分治算法实现汉诺塔

这里问题最小规模 按 n==2 考虑，当 n>2 时，把上层的塔看做一个整体

```java
package com.learn.fenzhi;

public class hannoitower
{

// 把n-1个盘子由A 移到 B；
// 把第n个盘子由 A移到 C；
// 把n-1个盘子由B 移到 C；
public static void play(int num,char a,char b,char c)
    {
        if(num==1)
        {
            //只有一个盘直接从a移到c
            System.out.println("第一个盘从"+a+"->"+c);
        }else{
            //盘>=2 总是看成两个盘
            //把最上面的盘全部移到b
            play(num-1,a,c,b);
            System.out.println("第"+num+"个盘从"+a+"->"+c);
            //把b上面移到c
            play(num-1,b,a,c);
        }
    }

}

```

### 动态规划算法

#### 核心思想

- 将大问题规划分为小问题进行解决，从而一步步虎丘最优解的处理算法
- 动态规划算反与分治算法类似，其基本思想也是将带球解问题分解成若干个子问题，先求解子问题，然后从这些子问题的解的到原问题的解
- 与分治法不同的是，适合用于动态规划求解的问题，经分级得到子问题往往不是互相独立的。即下一个阶段的求解是建立在上一阶段的解的基础上进行进一步求解
- 动态规划可以通过填表方式来逐步推进，得到最优解。

#### 背包问题
