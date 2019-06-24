---
title: Hi Spring Boot ~
date: 2019-06-23 22:18:06
tags: [Spring Boot]
---

## 学习笔记 : 拥抱Spring Boot
*简介 : 随着注解的功能增强,尤其是`Servlet 3.0`规范的提出,Web容器可以脱离`web.xml`的部署,使得Web容器完全可以基于注解开发,对于`Spring 3.x`和`Spring 4.x`的版本注解功能越来越强大,对于`XML`的依赖越来越少,到了4.x的版本后甚至可以完全脱离XML,因此在Spring中使用注解开发占据了主流的地位. 于此同时,Pivotal团队在原有Spring基础上主要通过注解的方式继续简化了Spring框架的开发,他们基于Spring框架开发了`Spring Boot`,所以Spring Boot并非是代替Spring框架,而是让Spring框架更加容易使用哟 ~*


### Spring Boot的优点
- *创建独立的Spring应用程序*
- *嵌入的Tomcat,Jetty或Undertow,无须部署WAR文件*
- *允许通过Maven来根据需要获取`starter`*
- *尽可能地自动配置Spring*
- *提供生产就绪型功能,如指标,健康检查和外部配置*
- *绝对没有代码生成,对XML没有要求配置*


### 第一个Spring Boot程序
1. *DemoApplication.java : Spring boot启动类*
```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

}
```

2. *MyController.java : Spring mvc控制器*
```java
package com.example.demo.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @project: first-spring-boot
 * @description: 自定义控制器
 * @author: 黄宇辉
 * @date: 6/23/2019-9:59 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@RestController
public class MyController {

    @GetMapping("/hello")
    public String hello() {
        return "Hi Spring Boot! it's so simple ~";
    }
}
```

3. *启动项目的方式有三种,如下所示. 启动成功后在浏览器中输入:`http://localhost:8080/hello`即可看到如下图的运行结果.*
1. *使用Maven命令运行项目 : `mvn spring-boot:run`*
2. *直接运行DemoApplication.java中的main方法*
3. *打包运行 : 将Spring boot打成jar包后使用`mvn package`运行*

![ ](Hi-Spring-Boot/Hi-Spring-Boot.PNG)


### 推荐书籍
- *深入浅出Spring Boot 2.x ——杨开振 · 著*
- *Spring Boot+Vue全栈开发实战 ——王松 · 著*