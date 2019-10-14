---
title: JDK 8新特性之默认方法的基本使用
date: 2019-08-19 20:41:39
tags: [JDK]
---

## 学习笔记 : JDK 8新特性之默认方法的基本使用

### 简介
*Java 8 新增了接口的默认方法,简单点说既默认方法就是接口可以有实现方法,而且不需要实现类去实现其方法. 我们只需在方法名前面加个 default 关键字即可实现默认方法的定义哟~ 那为什么要引入这个特性呢 ? 解答如下 :*
```
首先,之前的接口是个双刃剑,好处是面向抽象而不是面向具体编程. 缺陷是当需要修改接口时候,需要修改全部实现该接口的类,目前的 java 8 之前的集合框架没有 foreach 方法,通常能想到的解决办法是在JDK里给相关的接口添加新的方法及实现. 然而,对于已经发布的版本,是没法在给接口添加新方法的同时不影响已有的实现. 所以引进的默认方法,他们的目的是为了解决接口的修改与现有的实现不兼容的问题
``` 

*接口默认方法的`类优先`原则 : 若一个接口中定义了一个默认方法,而另一个父类或接口中又定义了一个同名的方法时,可分为情况 :*
* 选择父类中的方法 : 如果一个父类提供了具体的实现,那么接口中具有相同名称和参数的默认方法会被忽略
* 接口冲突 : 如果一个父接口提供了一个默认方法,而另一个接口也提供了一个具有相同名称和参数列表的方法( 不管方法是否为默认方法 ),那么必须覆盖该方法来解决冲突

*语法格式如下所示 :*
```java
public interface Vehicle {
   default void print(){
      System.out.println("this is a car");
   }
}
```


### 多个默认方法
*一个接口有默认方法,考虑这样的情况,一个类实现了多个接口,且这些接口有相同的默认方法,以下实例说明了这种情况的解决方法 :*
```java
public interface Vehicle {
   default void print(){
      System.out.println("this is a car");
   }
}
 
public interface FourWheeler {
   default void print(){
      System.out.println("this is a brougham");
   }
}
```

*第一个解决方案是创建自己的默认方法,来覆盖重写接口的默认方法 :*
```java
public class Car implements Vehicle, FourWheeler {
   default void print(){
      System.out.println("this is a motorcycle");
   }
}
```

*第二种解决方案可以使用 super 来调用指定接口的默认方法 :*
```java
public class Car implements Vehicle, FourWheeler {
   public void print(){
      Vehicle.super.print();
   }
}
```


### 静态默认方法
*Java 8 的另一个特性是接口可以声明( 并且可以提供实现 )静态方法,例如 :*
```java
public interface Vehicle {
   default void print(){
      System.out.println("this is a car");
   }

   static void blowHorn(){
      System.out.println("sound the horn");
   }
}
```


### 默认方法实例
*通过以下代码来了解下关于默认方法的使用吧~*
```java
package pers.huangyuhui.jdk8.method;


import org.junit.Test;

interface Vehicle {
    default void print() {
        System.out.println("this is a car");
    }

    static void blowHorn() {
        System.out.println("sound the horn");
    }
}

interface FourWheeler {
    default void print() {
        System.out.println("this is a brougham");
    }
}

class Car implements Vehicle, FourWheeler {
    public void print() {
        Vehicle.super.print();
        FourWheeler.super.print();
        Vehicle.blowHorn();
        System.out.println("this is a motorcycle");
    }
}

/**
 * @project: jdk-learnig
 * @description: 默认方法实例
 * @author: 黄宇辉
 * @date: 8/19/2019-9:11 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class DefaultMethod {

    @Test
    public void test() {
        Vehicle vehicle = new Car();
        vehicle.print();
    }
}
```

*程序运行结果如下所示 :*
```
this is a car
this is a brougham
sound the horn
this is a motorcycle
```



*参考 《Java 8 默认方法》 : https://www.runoob.com/java/java8-default-methods.html*