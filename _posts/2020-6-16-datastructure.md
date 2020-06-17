---
title: 哈希表
author: 晓
date: 2020-6-15 11:16:00 +0800
categories: [DataStructure]
tags: [hashmap,java]
--- 

## 哈希表

根据关键码值（key value）而直接进行访问的数据结构
它通过把关键码值映射到表中一个位置来访问记录，以加快查找的速度，
这个映射函数叫做散列函数，存放记录的数组叫做散列表。

常见的哈希表结构
数组+链表
数组+二叉树

哈希表可以同时管理多条链表

![哈希表]({{ "/assets/img/sample/hashmap.png" | relative_url }}) 

``` java
package com.learn.hash;
import java.util.Scanner;

public class HashTab {
    public static void main(String[] args) {

        //创建哈希表
        HashTable hashTab = new HashTable(7);

        //写一个简单的菜单
        String key = "";
        Scanner scanner = new Scanner(System.in);
        while(true) {
            System.out.println("add:  添加雇员");
            System.out.println("list: 显示雇员");
            System.out.println("find: 查找雇员");
            System.out.println("exit: 退出系统");

            key = scanner.next();
            switch (key) {
                case "add":
                    System.out.println("输入id");
                    int id = scanner.nextInt();
                    System.out.println("输入名字");
                    String name = scanner.next();
                    //创建 雇员
                    Emp emp = new Emp(id, name);
                    hashTab.Add(emp);
                    break;
                case "list":
                    hashTab.List();
                    break;
                case "find":
                    System.out.println("请输入要查找的id");
                    id = scanner.nextInt();
                    //hashTab.findEmpById(id);
                    break;
                case "exit":
                    scanner.close();
                    System.exit(0);
                default:
                    break;
            }
        }

    }
}


class HashTable{
    EmpLinkedList[] empLinkedListArray;
    private int size;
    public HashTable(int size)
    {
        empLinkedListArray =new EmpLinkedList[size];
        for(int i=0;i<size;i++)
        {
            EmpLinkedList list =new EmpLinkedList();
            empLinkedListArray[i]=list;
        }
        this.size=size;
    }

    public void Add(Emp value)
    {
        EmpLinkedList list = empLinkedListArray[HashFun(value.id)];
        list.Add(value);
    }

    //散列函数
    public int  HashFun(int id)
    {
        return id%size;
    }

    public void List()
    {
        for(int i=0;i<empLinkedListArray.length;i++)
        {
            empLinkedListArray[i].List();
        }
    }

}


class Emp{
    public int id;
    public String name;
    public Emp(int id,String name)
    {
        this.id=id;
        this.name=name;
    }
    public void ToString()
    {
        System.out.println("id:"+id+" name:"+name);
    }
    public Emp next;

}

class EmpLinkedList
{
    public Emp head;

    public EmpLinkedList()
    {
        head=new Emp(0,null);
    }

    public void Add(Emp value)
    {
        Emp  temp = head;
        while(temp.next!=null)
        {
            temp = temp.next;
        }
        temp.next=value;
    }

    //list 所有元素
    public void List()
    {
        Emp temp=head.next;
        while (temp!=null)
        {
            temp.ToString();
            temp=temp.next;
        }
    }
}
```
