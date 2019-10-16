---
title: 详解JDK 12中的新特性
date: 2019-10-14 11:11:51
tags: [JDK]
---

## 学习笔记 : 详解JDK 12中的新特性

### 简介
*JDK 12 is the open-source reference implementation of version 12 of the Java SE Platform as specified by by `JSR 386` in the Java Community Process. JDK 12 reached General Availability on 19 March 2019. Production-ready binaries under the GPL are available from Oracle; binaries from other vendors will follow shortly. The features and schedule of this release were proposed and tracked via the JEP Process, as amended by the `JEP 2.0 proposal`. The release was produced using the `JDK Release Process (JEP 3)`.*



### 新特性
* 189: 	Shenandoah: A Low-Pause-Time Garbage Collector (Experimental)
* 230: 	Microbenchmark Suite
* 325: 	Switch Expressions (Preview)
* 334: 	JVM Constants API
* 340: 	One AArch64 Port, Not Two
* 341: 	Default CDS Archives
* 344: 	Abortable Mixed Collections for G1
* 346: 	Promptly Return Unused Committed Memory from G1



### 时间表
* 2018/12/13 		Rampdown Phase One (fork from main line)
* 2019/01/17 		Rampdown Phase Two
* 2019/02/07 		Release-Candidate Phase
* 2019/03/19 		General Availability



### Switch Expressions 特性演示
```java
int score = new Random(100).nextInt();

/*
JDK12之前版本的写法:许多break使它不必要地冗长,如果忘记写break,则会产生意料之外的结果或者异常,所以针对于此jdk12在这里进行了优化升级
*/
switch (score) {
    case 60:
        System.out.println("及格");
        break;
    case 80:
        System.out.println("良好");
        break;
    case 90:
        System.out.println("优秀");
        break;
    default:
        System.out.println("default");
        break;
}

/*
JDK12新特性写法:我们建议引入一种新形式的开关标签,写成"case L ->"表示如果标签匹配,则只执行标签右侧的代码:
*/
switch (score) {
    case 60 -> System.out.println("及格");
    case 80 -> System.out.println("良好");
    case 90 -> System.out.println("优秀");
    default -> System.out.println("default");
}

/*
JDK12前的写法:许多Switch表达式,每个case都会赋值给一个变量或者执行某种操作,如下是赋值给numLetters变量具体值:
*/
int numLetters;
switch (score) {
    case 60:
    case 70:
        numLetters = 6;
        break;
    case 80:
    case 90:
        numLetters = 7;
        break;
    case 100:
        numLetters = 8;
        break;
    default:
        throw new IllegalStateException("score:" + score);
}

/*
JDK12新特性写法:将此表达为一种陈述是迂回的,重复的,并且容易出错. 意味着我们应该计算numLetters每一天的价值,应该可以直接说,使用更清晰,更安全Switch表达式:
*/
int numLetters2 = switch (score) {
    case 60, 70 -> 6;
    case 80, 90 -> 7;
    case 95, 100 -> 8;
    default -> throw new IllegalStateException("count:" + score);
};

/*
如果将它用在方法上,则可以为写成:
*/
static void howMany(int k) {
    switch (k) {
        case 1 -> System.out.println("one");
        case 2 -> System.out.println("two");
        case 3 -> System.out.println("many");
}

/*
或者类上,例如将这个新特性用在抽象工厂类:
*/ 
T result = switch (arg) {
    case L1 -> e1;
    case L2 -> e2;
    default -> e3;
};
```



> 👍For more information, please refer to http://openjdk.java.net/projects/jdk/12/ and the document : https://docs.oracle.com/en/java/javase/12/
