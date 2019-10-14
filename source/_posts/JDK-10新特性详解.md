---
title: 详解JDK 10中的新特性
date: 2019-08-21 16:49:44
tags: [JDK]
---

## 学习笔记 : 详解JDK 10中的新特性
*2018年3月21日,Oracle官方宣布 Java10 正式发布. 需要注意的是 Java9 和 Java19 都不是 LTS( Long-Term-Support )版本,和过去的 Java 大版本升级不同,这两个只有两年半左右的开发和维护期. 而 Java11,也就是18.9LTS,才是继 Java8 之后的第一个 LTS 版本. JDK10 中一共定义了109个新特性,其中包含12个 JEP,还有一些新 API 和 JVM 规范以及 Java 语言规范上的改动,JDK10 的12个 JEP( JDK Enhancement Proposal 特性加强提议 )如下所示,更多信息请参阅官方文档 : http://openjdk.java.net/projects/jdk/10/*

1. Local-Variable Type Inference (`主要重点:局部变量类型推断`)
2. Consolidate the JDK Forest into a Single Repository (JDK库的合并)
3. Garbage-Collector Interface (统一的垃圾回收接口)
4. Parallel Full GC for G1 (为G1提供并行的Full GC)
5. Application Class-Data Sharing (应用程序类数据( AppCDS )共享)
6. Thread-Local Handshakes (threadLocal握手交互)
7. Remove the Native-Header Generation Tool (javah) (移除JDK中附带的javah工具)
8. Additional Unicode Language-Tag Extensions (使用附加的Unicode语言标记扩展)
9. Heap Allocation on Alternative Memory Devices (在可选内存设备上进行堆分配)
10. Experimental Java-Based JIT Compiler (使用基于Java的JIT编译器)
11. Root Certificates (根证书)
12. Time-Based Release Versioning (基于时间的发布版本)


### 局部变量类型推断
#### 前言
*很多人抱怨 Java 是一种强类型,需要引入大量的样板代码. 甚至在这些情况下,给定好变量名,通常很清楚发生了什么,明显类型声明往往被认为是不必要的. 许多流行的编程语言都已经支持某种形式的局部变量类型推断 : 如C++ (auto), C# (var), Scala (var/val), Go (declaration with :=)等*

#### 简介
*Java 局部变量类型推断( LVTI ),JDK10 中可以使用 var 作为局部变量类型推断标识符,`此符号仅适用于局部变量,增强for循环的索引,以及传统for循环的本地变量. 它不能使用于方法形式参数,构造函数形式参数,方法返回类型,字段,catch块形式参数或任何其他类型的变量声明`. 标识符 var 不是关键字,相反,它是一个保留的类型名称. 这意味着 var 用作变量,方法名或则包名称的代码不会受到影响. 但 var 不能作为类或则接口的名字( 但这样命名是比较罕见的,因为他违反了通常的命名约定,类和接口首字母应该大写 ). `其工作原理为 : 在处理 var 时,编译器先是查看表达式右边部分,并根据右边变量值的类型进行推断,作为左边变量的类型,然后将该类型写入字符码当中`*

#### 示例
*类型推断代码示例 :*
```java
var str = "ABC"; //根据推断为字符串类型
var l = 10L; //根据10L推断为long类型
var flag = true; //根据true推断为boolean类型
var flag1 = 1; //这里会推断boolean类型,0表示false非0表示true
var list = new ArrayList<String>(); //推断为ArrayList<String>
var stream = list.stream(); //推断为Stream<String>
```

*上述示例代码的反编译class文件 :*
```java
String str = "ABC";
long l = 10L;
boolean flag = true;
int flag1 = true;
ArrayList<String> list = new ArrayList();
Stream<String> stream = list.stream();
```

*遍历代码中使用类型推断 :*
```java
//集合遍历
var list = new ArrayList<String>();
for (var s : list) { }
//普通遍历
for (var i = 1; i < 10; i++) { }
```

*var 类型可以被final修饰符修饰*
```java
final int limit = 10;
```

#### 注意
*在局部变量使用中,这些情况不适用类型推断哟 : `没有初始化的局部变量声明`, `方法的返回类型`, `方法的参数类型`, `构造器的参数类型`, `属性`, `catch块`. 部分示例如下所示*

1. *初始值不能为null,既如果局部变量不赋值,则无法实现类型推断*
```java
//error
var string1;
var string2 = null;
```

2. *为数组静态初始化中,语法格式需要注意*
```java
int[] array = {1, 2, 3, 4, 5}; //正确写法
var array2 = new int[]{1, 2, 3, 4, 5}; //正确写法
var array3 = {1, 2, 3, 4, 5}; //错误写法
```

3. *Lambda表达式及方法引用中,左边的函数式接口不能声明为 var*
```java
//正确写法
Comparator<Integer> comparator = (o1, o2) -> Integer.compare(o1, o2);
//错误写法
var comparator = (o1, o2) -> Integer.compare(o1, o2);

//正确写法
Consumer<String> consumer = System.out::println;
//错误写法
var consumer = System.out::println;
```



*更多 JDK10 的新特性描述请参阅 : [官方文档](http://openjdk.java.net/projects/jdk/10/)*