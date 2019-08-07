---
title: Thymeleaf整合Shiro标签
date: 2019-08-06 15:16:26
tags: [Thymeleaf,Shiro]
---

## 学习笔记 : Thymeleaf整合Shiro标签
*详情请参考 : [一个简单的SpringBoot整合Shiro的权限管理案例](https://github.com/YUbuntu0109/Shiro-learning/tree/master/spring%20boot%20project%20with%20shiro)*

### 引入依赖
*添加Thymeleaf及其对Shiro扩展的依赖*
```xml
<!-- Thymeleaf -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<!-- Thymeleaf对shiro的扩展 -->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.0.0</version>
</dependency>
```

### 配置
*在Shiro的配置类中配置`ShiroDialect`*
```java
    /**
     * @description: 配置ShiroDialect, 用于thymeleaf和shiro标签配合使用
     * @date: 2019-08-05 6:37 PM
     * @return: at.pollux.thymeleaf.shiro.dialect.ShiroDialect
     */
    @Bean
    public ShiroDialect shiroDialect() {
        return new ShiroDialect();
}
```

### 添加HTML命令空间
```html
<html lang="zh_CN" xmlns:th="http://www.thymeleaf.org" xmlns:shiro="http://www.pollix.at/thymeleaf/shiro">
```

### Thymealf+Shiro标签
```html
<!-- guest标签:用户没有身份验证时显示相应信息,即游客访问信息 -->
<shiro:guest></shiro:guest>

<!-- user标签:用户已经身份验证/记住我登录后显示相应的信息 -->
<shiro:user></shiro:user>

<!-- authenticated标签:用户已经身份验证通过,即Subject.login登录成功,不是记住我登录的 -->
<shiro:authenticated></shiro:authenticated>

<!-- notAuthenticated标签:用户已经身份验证通过,即没有调用Subject.login进行登录,包括记住我,自动登录的也属于未进行身份验证 -->
<shiro:notAuthenticated></shiro:notAuthenticated>

<!-- principal标签:相当于((User)Subject.getPrincipals()).getUsername()  -->
<shiro: principal/>
<shiro:principal property="username"/>
　　
<!-- lacksPermission标签:如果当前Subject没有权限将显示body体内容 -->
<shiro:lacksPermission name="org:create"></shiro:lacksPermission>

<!-- hasRole标签:如果当前Subject有角色将显示body体内容 -->
<shiro:hasRole name="admin"></shiro:hasRole>

<!-- hasAnyRoles标签:如果当前Subject有任意一个角色（或的关系）将显示body体内容 -->
<shiro:hasAnyRoles name="admin,user"></shiro:hasAnyRoles>

<!-- lacksRole标签:如果当前Subject没有角色将显示body体内容 -->
<shiro:lacksRole name="abc"></shiro:lacksRole>　

<!-- hasPermission标签:如果当前Subject有权限将显示body体内容 -->
<shiro:hasPermission name="user:create"></shiro:hasPermission>
```



*🙂参考(非常感谢) : https://www.jianshu.com/p/c8b53445cbb0*