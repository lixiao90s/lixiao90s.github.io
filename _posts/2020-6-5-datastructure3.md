---
title: 栈
author: 晓
date: 2020-6-5 10:36:00 +0800
categories: [DataStructure]
tags: [stack,java]
--- 

## 栈
- 先入后出的有序列表 （FILO-First In Last Out）
- 栈是限制线性表中元素的插入和删除 只能在线性表一段进行的特殊线性表，允许插入和删除的一端称为栈顶，另一端为栈底
- 最先放入栈中元素在栈底，最后放入的元素在栈顶，删除则是最后放入的先删除，最先放入的最后删除。

## 栈涉及到的应用场景
- 子程序调用：在跳往子程序前，会先将下个指令的地址存放到堆栈中，子程序执行完毕后，从堆栈取出指令地址，回到之前程序
- 处理递归调用：和子程序的调用类似，只是出了存储下一个指令的地址外，也将参数，区域变量等数据存入堆栈
- 表达式的转换 [中缀表达式转后缀表达式]与求值
- 二叉树遍历
- 图形深度优先搜索算法（depth-first）

## 栈实现的计算器

``` java
package com.learn.stack;

///计算表达式
public class Caculator {
    public static void main(String[] args)
    {
        String exp = "3+2*6-5";
        int index =0;
        Stack numStack = new Stack(10);
        Stack expStack =new Stack(10);
        for(int i=0;i<exp.length();i++)
        {
            char c = exp.substring(i,i+1).charAt(0);
            //数栈 符号栈
            if(IsOper(c))
            {
                if(expStack.IsEmpty())
                {
                    expStack.Push(c);
                }else if(OperPriority(expStack.Peek()) <=OperPriority(c))
                {
                    expStack.Push(c);
                }else
                {
                    int value =Oper(numStack,expStack);
                    numStack.Push(value);
                    expStack.Push(c);
                }

            }else{
                //char 转 int (ascii)
                numStack.Push(c-48);
            }
        }
        //遍历完成后进行运算
        while(true)
        {
            if(expStack.IsEmpty())
                break;
            int value =  Oper(numStack,expStack);
            System.out.println("value="+value);
            numStack.Push( value);
        }
        System.out.printf("The result of expression %s is %d",exp,numStack.Peek());
    }

    //是否为运算符
    public static boolean IsOper(int oper)
    {
        if(oper=='*'||oper=='/'||oper=='+'||oper=='-')
        {
            return true;
        }
        return false;
    }

    //运算符优先级
    public static int OperPriority(int oper)
    {
        if(oper=='*'||oper=='/')
        {
            return 2;
        }else if (oper=='+'||oper=='-')
        {
            return 1;
        }
        return 0;
    }

    //运算规则
    public static int Oper(Stack numStack,Stack expStack)
    {
        int num1 = numStack.Pop();
        int num2 = numStack.Pop();
        int exp =expStack.Pop();
        int value=0;
        //num2 先入栈 运算时从左至右，num2在前
        switch (exp)
        {
            case '*':
                value=num2*num1;
                break;
            case '/':
                value=num2/num1;
                break;
            case '+':
                value=num2+num1;
                break;
            case '-':
                value=num2-num1;
                break;
        }
        return value;
    }
}

//数组实现栈
class Stack {
    //最大栈
    private int maxSize;
    //指示当前栈顶位置
    int top = -1;
    int[] stack;

    public Stack(int maxSize) {
        this.maxSize = maxSize;
        stack = new int[maxSize];
    }

    //栈是否已满
    public boolean IsFull() {
        return maxSize == top + 1;
    }

    //栈是否是空的
    public boolean IsEmpty() {
        return top == -1;
    }

    //出栈
    public int Pop() {
        if (IsEmpty()) {
            throw new RuntimeException("The stack is Empty");
        }
        int val = stack[top];
        top--;
        return val;
    }

    //压栈
    public void Push(int value) {
        if (IsFull()) {
            System.out.println("the stack is full push fail");
            return;
        }
        top++;
        stack[top] = value;
    }

    //查询栈顶信息
    public int Peek()
    {
        if(IsEmpty())
        {
            throw  new RuntimeException("empty stack");
        }
        return stack[top];
    }

    //遍历栈
    public void Show() {
        if (IsEmpty()) {
            return;
        }
        for (int i = top; i >= 0; i--) {
            System.out.printf("stack[%d]=%d\n", i, stack[i]);
        }
    }


}

```