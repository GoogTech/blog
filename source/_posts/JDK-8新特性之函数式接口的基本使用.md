---
title: JDK 8新特性之函数式接口的基本使用
date: 2019-08-18 08:48:00
tags: [JDK]
---

## 学习笔记 : JDK 8新特性之函数式接口的基本使用

### 简介
*`函数式接口`是整个 Lambda 表达式的一个根源,换句话来说 Java8 中的 Lambda 表达式要想彻底掌握,前提是要彻底理解好函数式接口哟,那么什么是函数接口呢 ? 答 : 函数式接口 (Functional Interface) 就是一个有且仅有一个抽象方法,但是可以有多个非抽象方法的接口,函数式接口可以被隐式转换为 Lambda 表达式. 注 : 我们可以通过在接口上使用 `@FunctionalInterface` 注解来检查它是否是一个函数接口. Java8中丰富的函数式接口都定义在 Java.util.function 包中哟~*


### Java内置四大核心函数式接口

|        函数式接口        | 参数类型 | 返回类型 |                                    用途                                     |
| :----------------------: | :------: | :------: | :-------------------------------------------------------------------------: |
|  `Consumer<T>` 消费性接口  |    T     |   void   |             对类型为T的对象应用操作,包含方法: void accept(T t)              |
|  `Supplier<T>` 供给性接口  |    无    |    T     |                     返回类型为T的对象,包含方法: T get()                     |
| `Function<T,R>` 函数型接口 |    T     |    R     |   对类型为T的对象应用操作,并返回结果是R类型的对象,包含方法: R apply(T t)    |
| `Predicate<T>` 断定性接口  |    T     | boolean  | 确定类型为T的对象是否满足某约束,并返回boolean值,包含方法: boolean test(T t) |


### 四大核心函数式接口程序示例
*消费性接口的程序示例 :*
```java
package pers.huangyuhui.function;

import org.junit.Ignore;
import org.junit.Test;

import java.util.function.Consumer;

/**
 * @project: jdk-learnig
 * @description: Java 8中函数式接口的基本使用
 * @author: 黄宇辉
 * @date: 8/18/2019-9:30 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class functionTest {

    //四大核心函数式接口:模拟消费性接口
    public void consumer(double monery, Consumer<Double> consumer) {
        consumer.accept(monery);
    }

    //未使用Lambda表达式
    @Test
    @Ignore
    public void testConsumer() {
        consumer(100.0, new Consumer<Double>() {
            @Override
            public void accept(Double monery) {
                System.out.println("costed monery : " + monery);
            }
        });
    }

    //使用Lambda表达式
    @Test
    public void testConsumerWithLamdba() {
        //可简写为: consumer(100.0, m -> System.out.println("costed monery : " + m));
        consumer(100.0, (Double m) -> System.out.println("costed monery :" + m));
    }
}
```

*断定性接口的程序示例 :*
```java
package pers.huangyuhui.function;

import org.junit.Ignore;
import org.junit.Test;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.function.Consumer;
import java.util.function.Predicate;

/**
 * @project: jdk-learnig
 * @description: Java 8中函数式接口的基本使用
 * @author: 黄宇辉
 * @date: 8/18/2019-9:30 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class functionTest {

    //四大核心函数式接口:模拟断定性接口
    public List<String> predicate(List<String> list, Predicate<String> p) {
        ArrayList<String> arrayList = new ArrayList<>();
        for (String s : list) {
            if (p.test(s)) {
                arrayList.add(s);
            }
        }
        return arrayList;
    }

    //未使用Lambd表达式
    @Test
    @Ignore
    public void testPredicate() {
        List<String> list = Arrays.asList("ab", "bc", "abc", "bcd", "abcd", "bcde");
        List<String> filterList = predicate(list, new Predicate<String>() {
            @Override
            public boolean test(String s) {
                return s.contains("a");
            }
        });
        System.out.println(filterList);
    }

    //使用Lambda表达式
    @Test
    public void testPredicateWithLambda() {
        List<String> list = Arrays.asList("ab", "bc", "abc", "bcd", "abcd", "bcde");
        //可简写为: List<String> filterList = predicate(list, s -> s.contains("a"));
        List<String> filterList = predicate(list, (String s) -> s.contains("a"));
        System.out.println(filterList);
    }

}
```