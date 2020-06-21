---
title: 'error : java.net.SocketException: Permission denied'
date: 2020-06-21 12:33:33
tags: [Exception,Java]
---

*昨天计划在远程服务器 Ubuntu18.04 上部署 `vsblog` 项目，当执行 `jave -jar vsblog.jar` 来启动项目时抛出了如下错误信息( 要知道在此之前我已经在本地` windows10` 操作系统上测试过了，一切 ok的啊 )*
```java
. . .
Protocol handler start failedCaused by: java.net.SocketException: Permission denied
```

*在这个异常之前还有很多错误信息，所以在此之前我 google 了很多资料也没能解决，直到我 google 如上所述的一条错误信息才找到了正确的解决方法，如下所述 :*


*摘自 stackflow : Linux doesn’t allow a normal user to bind to a TCP port that’s <= 1024. There’s a discussion of the reasons for that [here](https://unix.stackexchange.com/questions/16564/why-are-the-first-1024-ports-restricted-to-the-root-user-only). You’re attempting to bind to 86 and, therefore, it’s failing with “Permission denied”. The quickest and safest solution is to configure the port with a value that’s > 1024. As you’re using Boot’s embedded Tomcat instance, the port’s configured in application.properties using the server.port property.*


*译文 : Linux不允许普通用户绑定 "<= 1024" 的TCP端口。 这有一个关于原因的讨论[这里](https://unix.stackexchange.com/questions/16564/why-are-the-first-1024-ports-restricted-to-the-root-user-only)。 所以这也就是为什么当你试图将端口绑定到80，它会抛出“权限不足”的错误信息的原因。最快和最安全的解决方案就是使用大于1024的值来配置端口。例如当你使用SpringBoot中的嵌入式Tomcat实例时，你可以通过配置application.properties中的server.port属性来指定端口号。*
