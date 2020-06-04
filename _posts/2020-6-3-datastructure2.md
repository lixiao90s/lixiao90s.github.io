---
title: 链表
author: 晓
date: 2020-6-3 10:36:00 +0800
categories: [DataStructure]
tags: [linkedlist,java]
--- 

## 链表
- 链表是以节点方式存储
- 每个节点包含data，next域指向下一个节点
- 链表的各个节点不一定是连续存储
- 链表根据需求，可以带头结点也可以不带


![链表结构]({{ "/assets/img/sample/data_structure4.png" | relative_url }}) 
![链表结构]({{ "/assets/img/sample/data_structure5.png" | relative_url }}) 

## 尾部插入

``` java
package com.learn.linkedlist;

public class SingleLinkedListDemo {

    public static void main(String[] args){
        SingleLinkedList linkedList = new SingleLinkedList();
        for (int i=1;i<=10;i++)
        {
            HeroNode node = new HeroNode(i,"nick"+i);
            linkedList.Add(node);
        }
        linkedList.List();
    }
}

class HeroNode{
    int no;
    String nickName;
    HeroNode next;
    public HeroNode(int no,String nickName)
    {
        this.no=no;
        this.nickName=nickName;
    }
    public void ToString()
    {
        System.out.print("no="+no+" nickName="+nickName+" ");
    }
}

class SingleLinkedList{
    //头结点 固定，不可更改
    HeroNode head=new HeroNode(0,null);

    //添加节点
    public void Add(HeroNode node)
    {
        HeroNode tmp = head;
        while (true)
        {
            if(tmp.next==null)
            {
                tmp.next=node;
                break;
            }
            tmp=tmp.next;
        }
    }

    //显示所有的节点
    public void List()
    {
        HeroNode tmp=head;
        while(true)
        {
            if(tmp==null)
            {
                break;
            }
            tmp.ToString();
            tmp=tmp.next;
        }
    }

}
```
## 删除节点

只需要将目标节点的上一节点的next指向目标节点的next，即可删除目标节点

``` java
 //删除目标节点
    public void Delete(int no)
    {
        HeroNode tmp = head.next;
        while(true)
        {
            if(tmp==null || tmp.next==null)
            {
                break;
            }else if(tmp.next.no==no)
            {
                 tmp.next=tmp.next.next;
                 break;
            }
            //后移
            tmp=tmp.next;
        }
    }
```

## 获取有效节点个数
``` java
    public int GetLength()
    {
        HeroNode cur =head.next;
        int length=0;
        while(cur!=null)
        {
            length++;
            cur=cur.next;
        }
        return length;
    }
```
## 获取倒数第K个节点

相当于获取有效节点个数后，减去k后即正序的节点

``` java
public HeroNode getReverseNode(int index) {

        int size =GetLength();
        if(index>size || index<0)
        {
            return null;
        }
        int reverse_index=size-index;
        HeroNode tar =head.next;
        while(true)
        {
            if(tar==null)
            {
                break;
            }
            reverse_index--;
            if(reverse_index==0)
            {
                break;
            }
            tar=tar.next;
        }
        return tar;
    }
```

## 单链表反转 简单面试题
思路：    
- 新增头节点
- 遍历旧链表所有节点
- 每次遍历出的节点，都往新链表的头部插入
- 将原来链表头结点，指向新链表的头结点

``` java
public void Reverse()
    {
        HeroNode reverseHead= new HeroNode(0,null);
        HeroNode tmp = head.next;
        while (tmp!=null)
        {
            HeroNode next_tmp= tmp.next;
            tmp.next = reverseHead.next;
            reverseHead.next=tmp;
            tmp=next_tmp;
        }
        head.next=reverseHead.next;
    }
```

## 双向链表

双向链表是单链表扩展出来的结构，很多操作相同。
例如获取长度，查找元素。由于多了一个指向前驱元素的指针，在添加和删除时，向对于单链表会增加对前驱指针的操作。


与单向链表对比
- 单项链表，查找方向只能是一个方向，双向链表可以向前或者向后查找
- 单项链表不能自我删除，需要辅助接点，而双向链表，则可以自我删除（单向链表
删除时总是需要一个辅助节点，这个节点是待删除节点的前一个节点）
- 多存储了一个指针，内存消耗增加。

## 环形链表
 
### 创建

- 借助辅助指针first ，记录第一个节点，只有一个节点时，自己指向自己，形成闭环
- cur记录最后一次添加的节点，cur指向新增节点，新增节点next指向first

``` java
//添加节点
    public void Add(CNode cNode)
    {
        if(first==null)
        {
            first = cNode;
            first.next=first;
        }
        //将最后一个添加的节点后继指向新节点
        if(cur != null)
        {
            cur.next=cNode;
        }

        //形成闭环
        cNode.next=first;
        //记录节点
        cur=cNode;

    }
```

### 遍历环形链表

``` java
public void List()
    {
        //空表
        if(first==null)
        {
            return;
        }
        CNode tmp = first;
        while (true)
        {
            tmp.ToString();
            if(tmp.next==first)
            {
                break;
            }
            tmp=tmp.next;
        }
    }
```

### 约瑟夫问题
helper 类似于单链表删除节点时辅助节点，first指向需要删除节点，helper用于删除和判断循环结束条件

``` java
/**
     * 约瑟夫问题
     * @param k 起始位置
     * @param m 间隔
     */
    public void JosePhu(int k,int m)
    {
        //helper始终first的前一个元素
        CNode helper=first;
        while (true)
        {
            //定位helper
            //只有一个元素时first == helper
            if(helper.next==first)
            {
                break;
            }
            helper=helper.next;
        }
        //定位helper和first初始位置，从第k个开始,需要移动k-1次
        for(int i=1;i<=k-1;i++)
        {
            helper=helper.next;
            first=first.next;
        }
        //开始做出队处理，直到只剩下一个元素
        while(true)
        {
            if(helper==first)
            {
                helper.ToString();
                break;
            }
            for(int j=1;j<=m-1;j++)
            {
                helper=helper.next;
                first=first.next;
            }
            first.ToString();
            first = first.next;
            helper.next=first;
        }
    }
```
