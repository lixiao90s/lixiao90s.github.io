---
title: 递归
author: 晓
date: 2020-6-5 10:36:00 +0800
categories: [DataStructure]
tags: [recursion,java]
--- 

## 递归 -自己调用自己的函数 

### 使用场景
- 8皇后，汉诺塔，阶乘，迷宫
- 快排，归并，二分，分治
- 使用栈解决的问题->递归代码比较简洁    

### 使用递归需要遵守的问题
- 执行一个方法时，就创建一个新的受保护的独立空间（栈空间）
- 方法的局部变量是独立的，不会相互影响
- 每个递归定义至少必须有一个条件，满足时递归不再进行，而是返回值退出
- 遵守谁调用，就将结果返回谁，方法执行完毕或者返回时，该方法也就执行完毕
- 如果方法中，用到引用类型的变量，则会共享

## 迷宫回溯
1.成功条件 是终点可以走的通 即状态2
2.中间有个分支如果出现走不通，即开始回溯，分支即判断false，所有分支都判断为false则该点 即为3
3.递归至终点，并且可以走通，回溯完成当前路径，则是行的通的路径


``` java
package com.learn.recursion;

//迷宫回溯
public class Maze {
    public static void main(String[] args) {
        //数组标记 0 可走节点 ，1 障碍物， 2 走的通的节点 ，3 走不通的节点
        var map = CreateMaze(6,7);
        int sizeX=map.length;
        int sizeY=map[0].length;
        //设置障碍
        map[2][1]=1;
        map[2][2]=1;
        //开始走出迷宫
        GoMaze(map,1,1);
        for(int i=0;i<sizeX;i++) {
            for (int j = 0; j < sizeY; j++) {
                System.out.print(map[i][j]+" ");
            }
            System.out.println();
        }
    }

    //创建迷宫
    public static int[][] CreateMaze(int sizeX,int sizeY)
    {
        int[][] map= new int[sizeX][sizeY];
        for(int i=0;i<sizeX;i++)
            for(int j=0;j<sizeY;j++)
            {
                if(i==0||i==sizeX-1||j==0||j==sizeY-1)
                {
                    map[i][j]=1;
                }else{
                    map[i][j]=0;
                }
            }
        return map;
    }

    //走出迷宫
    //策略 下->右->上->左
    /*
     *x,y开始节点 map迷宫
     */
    public static boolean GoMaze(int[][] map,int sx,int sy)
    {
        for(int i=0;i<6;i++) {
            for (int j = 0; j < 7; j++) {
                System.out.print(map[i][j]+" ");
            }
            System.out.println();
        }
        System.out.println("-----------------------------------------");
        //终止循环
        if(map[4][5]==2)
        {
            return true;
        }else
        {
            if(map[sx][sy]==0)
            {
                map[sx][sy]=2;
                if(GoMaze(map,sx+1,sy))
                {
                    return true;
                }else if(GoMaze(map,sx,sy+1))
                {
                    return true;
                }else if(GoMaze(map,sx-1,sy))
                {
                    return true;
                }else if(GoMaze(map,sx,sy-1))
                {
                    return true;
                }
                map[sx][sy]=3;
                return  false;
            }else
            {
                return false;
            }
        }
    }
}

```

## 8皇后问题算法（回溯算法）

### 思路 
- 第一个皇后放在第一行第一列
- 第二个皇后放在第二行第一列，判断是否ok，如果不ok则继续往第二列第三列放
- 当得到一个正确解时，在栈回退到上一个栈时，就会开始回溯，即将第一个皇后放在第一个列时的正确解将全部得到
- 后续依次将第一个皇后放在第二列，第三列。。。
- 这里用一维数组表示棋盘 e.g arr[] ={1,1},下表表示index+1行，对应的值表示value+1列
  
### 实现
``` java
package com.learn.recursion;

//8皇后问题
public class Queen {
    public static  void main(String[] args)
    {
        Queen8 queen8=new Queen8();
        queen8.StarPut();
    }
}

class Queen8{
    //皇后数量
    int queen_num=8;
    int[] arr=new int[queen_num];

    public void StarPut()
    {
        PutQueen(0);
    }

    //放入棋子
    public void PutQueen(int n)
    {
        //n==8 即第九行的棋子，前面8个符合8皇后规则
        if(n==queen_num)
        {
            //递归完成，打印当前的棋子
            Log();
            return;
        }
        for(int i=0;i<queen_num;i++)
        {
            //把棋子放在第n+1行的第i+1列
            arr[n]=i;
            //如果符合 8皇后规则
            if(Check(n))
            {
                //则继续放下一行
                PutQueen(n+1);
            }
        }
    }

    //判断当前放入的列是否符合规则
    public boolean Check(int n)
    {
        for(int i=0;i<n;i++)
        {
            //arr[i]==arr[n] 列值相等
            //Math.abs(arr[i]-arr[n]在对角线上
           if(arr[i]==arr[n]||Math.abs(n-i)==Math.abs(arr[i]-arr[n]))
           {
               return false;
           }
        }
        return true;
    }

    //输出值
    public void Log()
    {
        for(int i=0;i<queen_num;i++)
        {
            System.out.print(arr[i]+" ");
        }
        //h换行
        System.out.println();
    }
}

```