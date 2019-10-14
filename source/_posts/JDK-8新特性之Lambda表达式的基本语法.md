---
title: JDK 8新特性之Lambda表达式的基本语法
date: 2019-08-17 16:40:31
tags: [JDK]
---

## 学习笔记 : JDK 8新特性之Lambda表达式的基本语法

### 简介
*Lambda表达式 : 在Java8语言中引入的一种新的语言元素和操作符,这个操作符为`->`,该操作符被称为`Lambda操作符`或`箭头操作符`,它将Lambda分为以下两个部分*
1. *`左侧` : 指定Lambda表达式需要的参数列表*
2. *`右侧` : 制定了Lambda体,是抽象方法的实现逻辑,也既Lambda表达式要执行的功能*


### 特征
* 可选类型声明 : 不需要声明参数类型,编译器可以统一识别参数值
* 可选的参数圆括号 : 一个参数无需定义圆括号,但多个参数需要定义圆括号
* 可选的大括号 : 如果主体包含了一个语句,就不需要使用大括号
* 可选的返回关键字 : 如果主体只有一个表达式返回值则编译器会自动返回值,大括号需要指定明表达式返回了一个数值


### 语法
1. 语法格式一 : 无参,无返回值
```java
Runnable runnable = () -> System.out.println("Hello Lambda1 !");
```

2. 语法格式二 : Lambda需要一个参数,但是没有返回值
```java
//可以简写为:  Consumer<String> consumer = System.out::println;
Consumer<String> consumer = (String str) -> System.out.println(str);
```

3. 语法格式三 : 数据类型可以省略,因为可由编译器推断得出,称为"类型推断"
```java
Consumer<String> c = (str) -> { System.out.println(str) };
```

4. 语法格式四 :  Lambda若需要一个参数时,参数的小括号可以省略
```java
Consumer<String> c = str -> { System.out.println(str) };
```

5. 语法格式五 : Lambda需要两个或以上的参数,多条执行语句,并且可以有返回值
```java
Comparator<String> c = (x,y) -> {
    System.out.println("this is comparator");
    return Integer.compare(x,y);
};
```

6. 语法格式六 : 当Lambda体只有一条语句时,return与大括号若有,都可以省略
```java
//可以简写为: Comparator<Integer> comparator = Integer::compare;
Comparator<Integer> c = (x,y) -> Integer.compare(x,y);
```

#### 程序示例
1. *程序示例 : 语法格式一*
```java
//未使用Lambda表达式
@Test
public void runnable() {
    Runnable runnable = new Runnable() {
        @Override
        public void run() {
            System.out.println("this is runnable");
        }
    };
    runnable.run();
}

//使用Lambda表达式
@Test
public void runnableWithLambda() {
    Runnable runnable = () -> System.out.println("this is runnable");
    runnable.run();
}
```

2. *程序示例 : 语法格式二/三/四*
```java
@Test
public void consumer() {
    Consumer<String> consumer = new Consumer<String>() {
        @Override
        public void accept(String s) {
            System.out.println(s);
        }
    };
    consumer.accept("this is consumer");
}

@Test
public void consumerWithLambda() {
    //可以简写为: Consumer<String> consumer = (str) -> System.out.println(str);
    //可以简写为:  Consumer<String> consumer = str -> System.out.println(str);
    //可以简写为: Consumer<String> consumer = System.out::println;
    Consumer<String> consumer = (String str) -> System.out.println(str);
    consumer.accept("this is consumer");
}
```

3. *程序示例 : 语法格式五*
```java
@Test
public void comparator() {
    Comparator<Integer> comparator = new Comparator<Integer>() {
        @Override
        public int compare(Integer x, Integer y) {
            return Integer.compare(x, y);
        }
    };
    System.out.println(comparator.compare(1, 2));
}


@Test
public void comparatorWithLambda() {
    Comparator<Integer> comparator = (x, y) -> {
        System.out.println("this is comparator");
        return Integer.compare(x, y);
    };
    System.out.println(comparator.compare(1, 2));
}
```

4. *程序示例 : 语法格式六*
```java
@Test
public void comparator() {
    Comparator<Integer> comparator = new Comparator<Integer>() {
        @Override
        public int compare(Integer x, Integer y) {
            return Integer.compare(x, y);
        }
    };
    System.out.println(comparator.compare(1, 2));
}

@Test
public void comparatorWithLambda() {
    //可以简写为: Comparator<Integer> comparator = Integer::compare;
    Comparator<Integer> comparator = (x, y) -> Integer.compare(x, y);
    System.out.println(comparator.compare(1, 2));
}
```