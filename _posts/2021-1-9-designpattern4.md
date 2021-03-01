---
title: java - 设计模式-原型模式
author: 晓
date: 2021-1-8 9:36:00 +0800
categories: [java]
tags: [java]
--- 


## 原型模式（ProtoType）

 - 用原型实例指定创建对象的种类，并且通过拷贝这些原型，创建新的对象
 - 原型模式是一种创建型模式，允许一个对象再创建另外一个可定制的对象，无需知道任何创建的细节
 - 工作原理是通过将一个原型对象传给那个要发动创建的对象，这个要发动创建的对象通过请求原型对象拷贝它们自己来实施创建，即对象.clone    
  
![图]({{"/assets/img/sample/protoType.png" | relative_url}}) 


``` java

package CreationalPattern.ProtoTypePattern;

public class Main {
    public static void main(String[] args)
    {
        ProtoType pt1 =new ProtoType(1);
        ProtoType pt2 = new ProtoType(2);
        pt1.setProtoType(pt2);

        System.out.println(pt1.toString());
        try {
            ProtoType pt3 = (ProtoType) pt1.clone();
            System.out.println(pt3.toString());
        }catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}

```  

``` java
package CreationalPattern.ProtoTypePattern;

/**
 * 原型模式
 */
public class ProtoType implements Cloneable {

    public int id;

    public void setProtoType(ProtoType protoType) {
        this.protoType = protoType;
    }

    private ProtoType protoType;



    public ProtoType(int id)
    {
        this.id = id;
    }

    @Override
    public String toString() {
       return "id:"+id+" protoType:"+protoType.hashCode();
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        ProtoType protoType=null;
        try {
           protoType = (ProtoType) super.clone();
        }catch (Exception e)
        {
            System.out.println(e.getMessage());
        }

        return protoType;
    }
}

``` 


## 输出结果

id:1 protoType:603742814
id:1 protoType:603742814

对于引用传递的对象再克隆的时候，传递的是地址，这里克隆模式使用的是**浅拷贝**

## 浅拷贝

- 对于数据类型是基本数据类型的成员变量，直接进行值传递
- 对于引用类型的成员变量，例如数组，或者成员对象，浅拷贝进行引用传递，也就是只会复制地址，**在一个对象修改该成员变量，会影响到另一个对象的该成员变量值**

## 深拷贝

- 复制对象的所有基本数据类型的成员变量值
- 为所还有引用数据类型的成员变量申请存储空间，并复制每个引用数据类型成员变量所引用的对象，直到该对象可达到所有的对象。

### 深拷贝实现 
- 重写clone方法来实现深拷贝
- 对象序列化实现深拷贝