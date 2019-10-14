---
title: JDK 9新特性概述
date: 2019-08-20 09:01:19
tags: [JDK]
---

### 学习笔记 : JDK 9新特性概述

### 简介
*⏳ 来看一下Java成立到Java 9版本发布的时间线吧,不得不说比我还大三岁哟~*

* 1990年初, 最初被命名为Oak
* 1995年5月23日, Java语言诞生
* 1996年1月, 第一个JDK-JDK1.0诞生
* 1996年4月, 10个最主要的操作系统供应商申明将在其产品中嵌入Java技术
* 1996年9月, 约8.3万个网页应用了Java技术来制作
* 1997年2月18日, JDK1.1发布
* 1997年4月2日, JavaOne会议召开, 参与者逾一万人, 创当时全球同类会议纪录
* 1997年9月, JavaDeveloperConnection 社区成员超过十万
* 1998年2月, JDK1.1被下载超过2,000,000次
* 1998年12月8日, Java 2企业平台J2EE发布
* 1999年6月, SUN公司发布Java三个版本 : 标准版(J2SE)、企业版(J2EE)和微型版(J2ME)
* 2000年5月8日, JDK1.3发布
* 2000年5月29日, JDK1.4发布
* 2001年6月5日, Nokia宣布到2003年将出售1亿部支持Java的手机
* 2001年9月24日, J2EE1.3发布
* 2002年2月26日, J2SE1.4发布, 此后Java的计算能力有了大幅提升
* 2004年9月30日, J2SE1.5发布, 成为Java语言发展史上的又一里程碑. 为了表示该版本的重要性, J2SE1.5更名为Java SE 5.0
* 2005年6月, JavaOne大会召开, SUN公司公开Java SE 6. 此时Java的各种版本已经更名, 以取消其中的数字"2" : J2EE更名为Java EE,J2SE更名为Java SE, J2ME更名为Java ME
* 2006年12月, SUN公司发布JRE6.0
* 2009年4月20日, 甲骨文以74亿美元的价格收购SUN公司, 取得java的版权, 业界传闻说这对Java程序员是个坏消息(其实恰恰相反)
* 2010年11月, 由于甲骨文对Java社区的不友善,因此Apache扬言将退出JCP
* 2011年7月28日, 甲骨文发布Java SE 7
* 2014年3月18日, 甲骨文发表Java SE 8
* 2017年7月, 甲骨文发表Java SE 9, 其带来了很多新特性, 其中最主要的变化是已经实现的模块化系统


### JDK 9新特新
1. `模块系统` : 模块是一个包的容器,Java 9 最大的变化之一是引入了模块系统(Jigsaw 项目)
2. `REPL (JShell)` : 交互式编程环境
3. `HTTP 2 客户端` : HTTP/2标准是HTTP协议的最新版本,新的 HTTPClient API 支持 WebSocket 和 HTTP2 流以及服务器推送特性
4. `改进的 Javadoc` : Javadoc 现在支持在 API 文档中的进行搜索,另外Javadoc 的输出现在符合兼容 HTML5 标准
5. `多版本兼容 JAR 包` : 多版本兼容 JAR 功能能让你创建仅在特定版本的 Java 环境中运行库程序时选择使用的 class 版本
6. `集合工厂方法` : List,Set 和 Map 接口中,新的静态工厂方法可以创建这些集合的不可变实例
7. `私有接口方法` : 在接口中使用private私有方法,我们可以使用 private 访问修饰符在接口中编写私有方法
8. `进程 API` : 改进的 API 来控制和管理操作系统进程. 引进 java.lang.ProcessHandle 及其嵌套接口 Info 来让开发者逃离时常因为要获取一个本地进程的 PID 而不得不使用本地代码的窘境
9. `改进的 Stream API` : 改进的 Stream API 添加了一些便利的方法,使流处理更容易,并使用收集器编写复杂的查询
10. `改进 try-with-resources` : 如果你已经有一个资源是 final 或等效于 final 变量,您可以在 try-with-resources 语句中使用该变量,而无需在 try-with-resources 语句中声明一个新变量
11. `改进的弃用注解 @Deprecated` : 注解 @Deprecated 可以标记 Java API 状态,可以表示被标记的 API 将会被移除,或者已经破坏
12. `改进钻石操作符(Diamond Operator)`  : 匿名类可以使用钻石操作符(Diamond Operator)
13. `改进 Optional 类` : java.util.Optional 添加了很多新的有用方法,Optional 可以直接转为 stream
14. `多分辨率图像 API` : 定义多分辨率图像API,开发者可以很容易的操作和展示不同分辨率的图像了
15. `改进的 CompletableFuture API` : CompletableFuture 类的异步机制可以在 ProcessHandle.onExit 方法退出时执行操作
16. `轻量级的 JSON API` : 内置了一个轻量级的JSON API
17. `响应式流(Reactive Streams) API` : Java 9中引入了新的响应式流 API 来支持 Java 9 中的响应式编程



*更多的新特性描述请参阅 : [What's New in JDK9](https://docs.oracle.com/javase/9/whatsnew/toc.htm)*