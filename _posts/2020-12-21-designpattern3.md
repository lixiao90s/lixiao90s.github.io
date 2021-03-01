---
title: 设计模式 - 单例
author: 晓
date: 2020-12-20 9:36:00 +0800
categories: [Design Pattern]
tags: [Disign Pattern]
--- 

## 单例模式
采取一定的方法保证在整个软件系统中，某个类只能存在一个对象实例，
并且该类只提供一个取得其对象实例的方法（静态方法）

### 八种方式
 - 饿汉式（静态常量）
 - 饿汉式（静态代码块）

 - 懒汉式（线程不安全）
 - 懒汉式（线程安全，同步方法）
 - 懒汉式（线程安全，同步代码块）

 - 双重检查
 - 静态内部类
 - 枚举



### 饿汉式（静态常量）
1.构造器私有化（防止new出新实例）
2.类的内部创建对象
3.向外暴露一个静态方法（获取实例）

``` java
public class Singleton7 
{
    private Singleton7()
    {

    }
    private static class SingletonInstance{
        private static final Singleton7 INSTANCE = new Singleton7();
    }

    public static  Singleton7 getInstance()
    {
        return SingletonInstance.INSTANCE;
    }
}
```


优点：写法简单，类装载的时候完成实例化，避免线程安全问题
缺点：类装载的时候完成实例化，没有lazy loading效果，如果自始至终没有用到单例，会造成内存浪费

这种方式基于classloader机制避免了多线程的同步问题，不过，intance在装载时就实例化，单例模式中大多数都是调用getInstance方法，但是导致类装载的原因很多，不能确定有其他的方式导致类装载，这时候初始化instance没有达到lazy loading的效果。


### 饿汉式（静态代码块）

``` java
packagepattern.Singleton;


/**
 * 饿汉式 静态代码块
 */
public class Singleton2 {
    private Singleton2()
    {

    }
    private  static Singleton2 instance;
    static {
        instance = new Singleton2();
    }
    public static Singleton2 getInstance()
    {
        return instance;
    }
}
```

类实例化的过程放在了静态代码块中，也是类装载的时候，就执行了静态代码块的中的代码，初始化类的实例。优缺点和上面相同。

### 懒汉式（线程不安全）

``` java
/**
 * 懒汉式 线程不安全 lazy loading
 */
public class Singleton3 {
    private Singleton3()
    {

    }
    private  static Singleton3 instance;

    public static Singleton3 getInstance()
    {
        if(instance==null){
            instance = new Singleton3();
        }
        return instance;
    }
}
```

1.起到了lazy loading的效果，但是只能单线程下使用.
2.如果在多线程下，多个线程同时进入if语句，就会产生多个实例

### 饿汉式（线程安全）
``` java
/**
 * 懒汉式，线程安全，同步方法
 */
public class Singleton4 {
    private Singleton4()
    {

    }
    private  static Singleton4 instance;

    public static synchronized Singleton4 getInstance()
    {
        if(instance==null){
            instance = new Singleton4();
        }
        return instance;
    }
}
```

优点：线程安全
缺点：效率太低，每个线程想要获取实例执行getInstance都要进行同步，但是该方法只需要执行一次，实例化后直接return就好了

实际开发不推荐


### 双重检查

``` java
/**
 * Double Check 双重检查
 * 优点： 线程安全，懒加载，getInstance方法效率也提高了
 */
public class Singleton6 {
    private Singleton6()
    {

    }
    private static volatile Singleton6 instance;

    public static  Singleton6 getInstance()
    {
        if(instance==null){
            synchronized(Singleton6.class) {
                if(instance==null) {
                    instance = new Singleton6();
                }
            }
        }
        return instance;
    }
}
```

推荐使用的方法
优缺点：
线程安全，延迟加载，效率较高 
第一个instance==null 不为空则直接返回，避免方法每次访问同步
第二个instance==null 保证了线程安全
这样保证了效率和线程安全，在实际开发中推荐这样使用

### 静态内部类

``` java
/**
 * 静态内部类方法
 */
public class Singleton7 
{
    private Singleton7()
    {

    }
    private static class SingletonInstance{
        private static final Singleton7 INSTANCE = new Singleton7();
    }

    public static  Singleton7 getInstance()
    {
        return SingletonInstance.INSTANCE;
    }
}
```

1.这种方式采用了类装载的机制来保证初始化实例只有一个线程
2.静态内部类在Singleton类被装载时并不会立即实例化，而是在需要实例化时，调用getInstance方法，才会装载静态内部类SingletonInstance，从而完成Singleton的实例化，提高了效率
3.类的静态属性只会在第一次加载类的时候初始化，所以在这里，JVM帮助我们保证了线程安全，在类进行初始化时，别的线程是无法进入的，避免线程不安全。

推荐使用

### 总结

- 单例模式保证系统中该类只有这一个对象，节省了系统资源，对于频繁创建销毁的对象，使用单例模式可以系统性能
- 当想使用一个实例时，只能用getInstance，不能new
- 单例模式使用的场景：需要频繁的进行创建和销毁的对象，创建对象时耗时过多或耗费资源过多（重量级对象），但又经常用到的对象
工具类对象，频繁访问数据库或文件的对象（比如数据源，session工厂等）

