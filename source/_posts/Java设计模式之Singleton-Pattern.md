---
title: Java设计模式之Singleton Pattern
date: 2019-09-06 13:32:05
tags: [Java,design and pattern]
---

## 学习笔记 : Java设计模式之Singleton Pattern

### 概念
*单例设计模式 : 确保一个类只能有一个实例,并提供一个全局访问点来访问这个实例. (Singleton Pattern : Ensure a class has only one intance,and provide a global point of access to it. ). 单例设计模式是一种对象创建型模式,其主要有三个要点 :*
1. *某个类只能有一个实例*
2. *必须自行创建这个实例*
3. *必须自行向系统提供这个实例*


### 示例程序-饿汉式单例
*饿汉式单例类( Eager Singleton )是最简单的单例类,既类被加载时静态变量 instance 就会被初始化,程序示例如下所示 :*
```java
package pers.huangyuhui.singleton_pattern.eager_singleton;

/**
 * @project: design-patterns
 * @description: 单例模式-饿汉式单例类
 * @author: 黄宇辉
 * @date: 9/6/2019-10:44 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class EagerSingleton {

    private static final EagerSingleton instance = new EagerSingleton();

    private EagerSingleton() {

    }

    public static EagerSingleton getInstance() {
        return instance;
    }
}
```

*注 : 从资源利用效率的角度来讲饿汉式单例不及懒汉式单例,而且系统加载时由于需要创建饿汉式单例对象,加载时间可能比较长.*


### 程序示例-懒汉式单例
*与饿汉式单例不同的是,懒汉式单例类( Lazy Singleton )在第一次被引用时才会将自己实例化,当单例类被加载时并不会将自己实例化, 示例程序如下所示 :*
```java
package pers.huangyuhui.singleton_pattern.lazy_singleton;

/**
 * @project: design-patterns
 * @description: 单例模式-懒汉式单例类
 * @author: 黄宇辉
 * @date: 9/6/2019-10:50 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class LazySingleton {

    //被volatile修饰的成员变量可以确保多个线程都能够正确处理
    private volatile static LazySingleton instance = null;

    private LazySingleton() {

    }

    public static LazySingleton getInstance() {
        //第一重判断
        if (instance == null) {
            //锁定代码块:确保任何时刻只有一个线程可执行此代码块
            synchronized (LazySingleton.class) {
                //第二重判断:防止产生多个单例对象
                if (instance == null) {
                    instance = new LazySingleton();
                }
            }
        }
        return instance;
    }
}
```

*注 : 由于`volatile`关键字会屏蔽Java虚拟机所做的一切代码优化,继而可能会导致系统的运行效率降低,因此即使使用双重检查锁定来实现单例模式也不是一种完美的实现方法哟~*


### 示例程序-内部类实现单例
*饿汉式单例不能实现延迟加载,不管将来用不用始终占据内存. 懒汉式单例类线程安全控制繁琐,而且性能受影响. 为了克服这些问题,在Java语言中可以通过使用 Initialization on Demand Holder( `IoDH` )技术来实现单例模式,示例程序如下所示 :*
```java
package pers.huangyuhui.singleton_pattern.IoDH_singleton;

/**
 * @project: design-patterns
 * @description: 单例模式-内部类实现单例类
 * @author: 黄宇辉
 * @date: 9/6/2019-11:11 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Singleton {
    private Singleton() {

    }

    //静态内部类
    public static class HolderClass {
        private final static Singleton instance = new Singleton();
    }

    public static Singleton getInstance() {
        return HolderClass.instance;
    }

}
```

*注 : 通过使用`IoDH`既可以实现延迟加载,又可以保证线程安全,不影响系统性能,既其为一种最好的Java语言单例模式实现方式,其缺点为很多面向对象语言并不支持IoDH哟~*