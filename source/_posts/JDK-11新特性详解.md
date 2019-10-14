---
title: 详解JDK 11中的新特性
date: 2019-08-22 09:17:34
tags: [JDK]
---

## 学习笔记 : 详解JDK 11中的新特性
*北京时间2018年9月26日,Oracle官方宣布 Java11 正式发布,这是 Java 大版本周期变化的第一个长期支持版本,非常值得关注. 从官网既可下载,最新发布的 Java11 将带来 ZGC, Http Client 等重要特性,一共包含17个 JEP (JDK Enhancement Proposals,既 JDK 增强方案提议), 其内容如下所示,更多详细信息请参阅 : http://openjdk.java.net/projects/jdk/11/*

1. 181: Nest-Based Access Control (嵌套类可见性控制)
2. 309: Dynamic Class-File Constants (动态文件常量)
3. 315: Improve Aarch64 Intrinsics (改进Aarch64 Intrinsics)
4. 318: Epsilon: A No-Op Garbage Collector (Epsilon:一个无操作的垃圾收集器)
5. 320: Remove the Java EE and CORBA Modules (删除Java EE和CORBA模块)
6. 321: HTTP Client (Standard) (`重点:标准HTTP客户端`)
7. 323: Local-Variable Syntax for Lambda Parameters (用于Lambda参数的局部变量语法)
8. 324: Key Agreement with Curve25519 and Curve448 (Curve25519和Curve448算法的密钥协议)
9. 327: Unicode 10
10. 328: Flight Recorder (飞行记录器)
11. 329: ChaCha20 and Poly1305 Cryptographic Algorithms (haCha20和Poly1305加密算法支持)
12. 330: Launch Single-File Source-Code Programs (启动单一文件的源代码程序)
13. 331: Low-Overhead Heap Profiling (低开销的Heap Profiling)
14. 332: Transport Layer Security (TLS) 1.3 (`重点:支持 TLS 1.3`)
15. 333: ZGC: A Scalable Low-Latency Garbage Collector (Experimental) (可伸缩低延迟垃圾收集器)
16. 335: Deprecate the Nashorn JavaScript Engine (弃用Nashorn JavaScript引擎)
17. 336: Deprecate the Pack200 Tools and API (弃用Pack200工具和API)


### String 新增方法
*新增加一系列字符串处理方法,程序示例如下 :*
```java
@Test
public void testString() {

    //判断字符串是否为空白
    var blank = " \t \n ".isBlank();
    //去除首尾空白
    var strip = " JavaStack ".strip();
    //去除尾部空格
    var trailing = "JavaStack ".stripTrailing();
    //去除首部空格
    var leading = " JavaStack".stripLeading();
    //复制字符串
    var repeat = "JavaStack".repeat(3);
    //行数统计
    var count = "A\nB\nC".lines().count();
    //输出结果
    List.of(blank, strip, trailing, leading, repeat, count).forEach(System.out::println);

}
```

*程序运行结果如下所示 :*
```
true
JavaStack
JavaStack
JavaStack
JavaStackJavaStackJavaStack
3
```


### 启动单一文件的源代码程序
*JDK11之前我们若运行一个 Java 源代码必须先编译,再运行,两个执行步骤. 而在 JDK11 版本中我们可以通过一个 Java 命令就直接搞定咯,如下所示 :*
```java
//JDK11版本之前需先编译后再运行程序
javac JavaStack.java
java JavaStack
//JDK11版本可直接运行程序
java JavaStack.java
```

*一个命令编译运行源代码的注意点 :*
* 只执行源文件中的第一个类,既第一个类必须包含主方法
* 不可以使用其它源文件中的自定义类( 本文件中的自定义类是可以使用的 )


### 优化局部变量类型推断
*在 var 上添加注解的语法格式在 JDK10 中是不能实现的,但在 JDK11 中加入了这样的语法*
```java
Consumer<String> consumer = (@Nonnull var s) -> s.toUpperCase();
```


### 删除 Java EE 和 CORBA 模块
*Java EE和 CORBA 两个模块在 JDK9 中已经标记`deprecated`,在 JDK11 中正式移除. JDK中 deprecated 的意思是在不建议使用,在未来的release版本会被删除. JavaEE由4部分组成 :*

* JAX-WS (Java API for XML-Based Web Services)
* JAXB (Java Architecture for XML Binding)
* JAF (the JavaBeans Activation Framework)
* Common Annotations

*这些特性和 JavaSE 关系不大,并且JavaEE被维护在 Github( https://github.com/javaee )中,版本同步造成维护困难. 最后,JavaEE 可以单独引用,Maven中心仓库也提供了 JavaEE( https://mvnrepository.com/artifact/javax/javaee-api ),所以没必要把JavaEE包含到JavaSE中. 而 CORBA( Common Object Request Broker Architecture,公共对象请求代理体系结构 )被标记为 Proposed Optional,既而表明将来可能会放弃对这些技术的必要支持*



*更多 JDK11 的新特性描述请参阅 : [JDK11 官方文档](https://docs.oracle.com/en/java/javase/11/)*