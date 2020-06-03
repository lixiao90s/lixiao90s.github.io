---
title: 稀疏数组与队列
author: 晓
date: 2020-6-1 17:36:00 +0800
categories: [DataStructure]
tags: [稀疏数组 , 队列]
--- 

参考资料：https://space.bilibili.com/302417610?spm_id_from=333.788.b_765f7570696e666f.2

## 稀疏数组
当一个数组中大部分元素是0，或者相同的值，可以使用稀疏数组来保存

处理方法：记录数组一共有几行几列，有多少个不同的值
把具有不同值得元素的行列及值记录在一个小规模数组中（稀疏数组），从而缩小程序的规模.

![稀疏数组]({{ "/assets/img/sample/data_structure1.png" | relative_url }}) 


## 队列
队列 
队列是个有序列表，可以用数组或者链表显示
遵循先入先出 （栈是先入后出），即存是加在队列尾部，取从数据头部

数组实现队列  front随着数据输出改变，rear随着数据输入改变

![数组实现队列]({{ "/assets/img/sample/data_structure2.png" | relative_url }}) 

### 数组实现队列

当前实现的队列是不能复用的，数组再使用一次后，不能重复添加

``` csharp
using UnityEngine;

namespace DataStructure {
    public class ArrayQueue {
        int max_size;
        int rear=-1; //指向数据输入前一位
        int front = -1;//指向数据输入后一位
        GameObject[] arr;//实现用的数组
        //Consturctor
        public ArrayQueue(int size)
        {
            max_size = size;
            arr= new GameObject[size];
        }

        public bool IsEmpty()
        {
            if(front==rear)
            {
                return true;
            }
            return false;
        }

        public bool IsFull()
        {
            return max_size<=front+1;
        }
        public bool Add(GameObject go)
        {
            if(IsFull())
            {
                Debug.LogError("Queue is Full");
                return false;
            }
            rear++;
            //超过上限
            if(rear>=arr.Length)
            {
                
                return false;
            }
            arr[rear]=go;
            return true;
        }

        public GameObject Get()
        {
            if(IsEmpty())
            {
                 Debug.LogError("Queue is Empty");
                return null;
            }
            front++;
            return arr[front];
        }

        public GameObject GetTop()
        {
            if(IsEmpty())
            {
                 Debug.LogError("Queue is Empty");
                return null;
            }
            return arr[front+1];
        }
        
    }
}
```

### 数组模拟环形队列

当队列为空时 front==rear，当队列满时，保留一个元素空间，也就是队列满时，数组中还有一个空闲单元
1.判断队列空 front==rear
2.判断队列满 (rear+1)%maxsize=front
3.队列有效长度（rear-front+maxsize）%maxsize


