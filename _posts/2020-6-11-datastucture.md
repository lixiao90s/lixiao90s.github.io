---
title: 排序算法
author: 晓
date: 2020-6-11 11:16:00 +0800
categories: [DataStructure]
tags: [sort,java]
--- 

## 排序
1.内部排序 将需要处理的所有数据都加载到内部存储器中进行排序
2.外部排序法 数据量过大，无法全部加载到内存

内部排序
- 插入排序
	- 直接插入排序
	- 希尔排序
- 选择排序
    -  简单排序
	- 堆排序
- 交换排序
	-  冒泡排序
	- 快速排序
	
- 归并排序
- 技术排序


## 冒泡排序


``` java 
int[] arr= new int[]{2,5,8,0};
        int temp =0;
        int count = arr.length-1;
        for (int j=1;j<=count;j++) {
            for (int i = 0; i < arr.length - j; i++) {
                if (arr[i] > arr[i + 1]) {
                    temp = arr[i];
                    arr[i] = arr[i + 1];
                    arr[i + 1] = temp;
                }
            }
        }
        for (var val:arr ) {
            System.out.println(val);
        }
```

进行优化，如果排序已经在完成，则不在继续

``` java
int[] arr= new int[]{2,5,8,0};
        int temp =0;
        int count = arr.length-1;
        boolean flag=false;
        for (int j=1;j<=count;j++) {
            //优化 某一次已经完成排序了，则不在进行排序
            flag=false;
            for (int i = 0; i < arr.length - j; i++) {
                if (arr[i] > arr[i + 1]) {
                    flag=true;
                    temp = arr[i];
                    arr[i] = arr[i + 1];
                    arr[i + 1] = temp;
                }
            }
            if(!flag){
                break;
            }
        }
        for (var val:arr ) {
            System.out.println(val);
        }
```

## 选择排序 
第一次从arr[0]-arr[n-1]选取最小值与arr[0]交换，第二次从arr[1]-arr[n-1]选取最小值和arr[1]交换，通过n-1次，获取到有序的队列

``` java 
for(int i=0;i<arr.length;i++)
        {
            for(int j=i;j<arr.length;j++)
            {
                if(arr[i]>arr[j])
                {
                    int temp =arr[i];
                    arr[i] = arr[j];
                    arr[j]=temp;
                }
            }

        }
```

## 插入排序

把n个带排序元素看成一个有序表和无需表，开始有序表只有一个元素，无序表包含n-1个元素，排序过程中每次去无序表中取一个元素，向有序表中寻找合适位置插入

``` java
  //插入排序实现
    public static void Sort(int[] arr)
    {
        int val =0;
        //记录插入的位置
        boolean flag =false;
        int index =0;
        //外层遍历有序列表
        for(int i=1;i<arr.length;i++)
        {
            val =arr[i];
            //初始插入位置
            index=i-1;
            //内层遍历有序列表，寻找插入位置
            while (index>=0&&val>arr[index])
            {
                arr[index+1]=arr[index];
                index--;
            }
            arr[index+1]=val;
        }
    }
```

### 希尔排序 缩小增量排序
希尔排序是把记录按下标的一定增量分组，对每组使用直接插入排序算法排序，随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1时，整个文件恰被分成一组，算法便终止，希尔排序对直接插入进行了优化。
``` java
//移位法
    public static void InsertSort2(int[] arr)
    {
        for(int gap=arr.length/2;gap>0;gap/=2)
        {
            for(int i=gap;i<arr.length;i++)
            {
                int index = i;
                int val = arr[i];
               // if(arr[i]<arr[index-gap]) {
                    while (index - gap >= 0 && val < arr[index-gap]) {
                        arr[index] = arr[index - gap];
                        index -= gap;
                    }
                //}
                arr[index]=val;
//                for(int j=i-gap;j>=0;j-=gap)
//                {
//                    if(arr[j]>arr[j+gap])
//                    {
//                        temp=arr[j];
//                        arr[j]=arr[gap+j];
//                        arr[gap+j]=temp;
//                    }
//                }
            }
        }
    }
```

## 快速排序

快速排序是对冒泡排序的改进，采用分治的思想

``` java
 //快速排序
    public void QuickSort(int[] arr)
    {
        QuickSort(arr,0,arr.length-1);
    }

    //快速排序
    public void QuickSort(int[] arr,int left,int right)
    {
        int pivot = partion(arr,left,right);
        if(left<right) {
            QuickSort(arr, left, pivot - 1);
            QuickSort(arr, pivot + 1, right);
        }
    }

    public int partion(int arr[],int left,int right)
    {
        int key =arr[left];
        while(left<right) {
            while (left < right && arr[right] >= key) {
                right--;
            }
            arr[left] = arr[right];
            while (left < right && arr[left] <= key) {
                left++;
            }
            arr[right]=arr[left];
        }
        arr[left]=key;
        return left;
    }
```

## 排序速度

![排序速度]({{ "/assets/img/sample/ds_sort.png" | relative_url }}) 
