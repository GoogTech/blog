---
title: '详解在IDEA中多处设置JDK及其区别'
date: 2019-08-13 09:56:45
tags: [IDEA]
---

## 学习笔记 : IDEA为何多处设置JDK,有什么区别呢 ?

### 设置编译器的版本
*编译器的版本,既生成class文件的jdk版本 : Settings > Build,Execution,Deployment > Complier > Java Compiler*
![](详解在IDEA中多处设置JDK及其区别/IDEA-JDK-Java-Complie.PNG)


### 设置Modules中的SDK和language level版本
*这里选择Modules中的SDK和language level的默认版本 : Project Structure > Project Settings > Project*
![](详解在IDEA中多处设置JDK及其区别/IDEA-JDK-Modules-SDK_Language-level.PNG)

*注意 : `Language Level为限定项目编译检查时最低要求的 JDK 特性`*


### 设置项目实际的Language Level版本
*Project Structure > Project Settings > Modules > Sources*
![](详解在IDEA中多处设置JDK及其区别/IDEA-JDK-Language-level.PNG)


### 设置项目实际的SDK版本
*相比整个工程设置语言级别可以更加地精确控制 : Project Structure > Project Settings > Modules > Dependencies*
![](详解在IDEA中多处设置JDK及其区别/IDEA-JDK-Module-SDK.PNG)


### ❓ IDEA为何多处设置JDK,有什么区别 ?
`project SDK` : 表示开发环境使用什么JDK来编译
`language level` :  表示项目最低可以使用什么版本的JDK通过

*至于说两者之间的区别,让我来给你举个例子 :若设置`project SDK`为1.8,`language level`设置为1.7,此时若使用lambda表达式,IDEA则会帮你标红并提醒你若这么写,项目未来可就不能使用1.7版本的JDK编译啦 ( `languang level用来保证代码最低可以被什么级别的 JDK 编译` ) ,尽管你使用的是SDK1.8 · · ·*