---
title: 设计模式 - 命令模式
author: 晓
date: 2020-5-28 9:36:00 +0800
categories: [Design Pattern]
tags: [Behavior Pattern,Command Pattern]
--- 

## 命令模式 

## UML图
![描边]({{ "/assets/img/sample/command_pattern1.png" | relative_url }})

## 作用
- 较容易的实现命令队列
- 需要的时候可以方便记入日志
- 接收请求的地方决定是否要接收日志
- 实现请求的撤销和重做
- 方便增加新的具体命令类

## 缺点
- 大量实例化命令

## 实现

``` csharp    
/// <summary>
/// 声明执行操作的接口
/// </summary>
abstract public class Command 
{
    //命令接收者
    protected CubeReciever reciever;
   abstract public bool Excute();
   abstract public bool Undo();
}
```

``` csharp
//子类 ConcreteCommand
using UnityEngine;
public class MoveCommand:Command
{
    Vector2 dir;
    public MoveCommand( CubeReciever reciever,Vector2 dir)
    {
        base.reciever=reciever;
        this.dir=dir;
    }
    public override bool Excute(){
        if(reciever==null || dir ==null)
        {
            return false;
        }
        reciever.CubeAction(dir);
        return true;
    }

    public override bool Undo(){
        if(reciever==null || dir==null)
        {
            return false;
        }
        reciever.CubeAction(-dir);
        return true;
    }


}

```

``` csharp
using UnityEngine;
public class CubeReciever 
{
    public GameObject go;
    public  CubeReciever(GameObject go)
    {
        this.go= go;
    }

    public void CubeAction(Vector2 dir){
        go.transform.localPosition+=new Vector3(dir.x,dir.y,0);
    }
}

```

``` csharp
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class PlayInvoker : MonoBehaviour {
    int command_pos = -1;
    //命令队列
    public List<MoveCommand> commands = new List<MoveCommand> ();
    public GameObject btn_up;
    public GameObject btn_down;
    public GameObject btn_undo;
    public GameObject btn_redo;
    private CubeReciever reciever;
    public GameObject reciever_go;
    void Awake () {
        reciever = new CubeReciever (reciever_go);
    }
    public void Click (GameObject go) {
        if (go.Equals (btn_up)) {
            Vector2 up = new Vector2 (0, 1);
            Move (up);
        } else if (go.Equals (btn_down)) {
            Vector2 down = new Vector2 (0, -1);
            Move (down);
        } else if (go.Equals (btn_undo)) {
            Undo ();
        } else if (go.Equals (btn_redo)) {
            Redo ();
        }
    }

    public void Move (Vector2 dir) {
        MoveCommand command = new MoveCommand (reciever, dir);
        bool suc = command.Excute ();
        if (suc) {
            commands.Add (command);
            command_pos = commands.Count;
        }

    }
    //实现请求的撤销和重做
    public void Undo () {
        int tar_pos = command_pos - 1;
        if (tar_pos >= 0 && tar_pos < commands.Count) {
            MoveCommand command = commands[tar_pos];
            if (command.Undo ()) {
                command_pos --;
            }
        }
    }
    //实现请求的撤销和重做
    public void Redo () {

        if(command_pos<commands.Count){
            MoveCommand command = commands[command_pos];
            if(command.Excute ())
            {
                command_pos++;
            } 
        }
      

    }
}
```