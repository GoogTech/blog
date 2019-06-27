---
title: Spring Boot之Thymeleaf
date: 2019-06-27 11:34:09
tags: [Spring Boot]
---

## 学习笔记 : Thymeleaf的常用语法
*简介 : Thymeleaf是一种用于Web和独立环境的现代服务器端的Java模板引擎. Thymeleaf的主要目标是将优雅的自然模板带到开发工作流程中,并将HTML在浏览器中正确显示,并且可以作为静态原型,让开发团队能更容易地协作. Thymeleaf能够处理HTML,XML,JavaScript,CSS甚至纯文本 .*


### 基础语法

#### 变量表达式 `${}`
*使用方法 : 直接使用`th:xx = "${}"`获取对象属性*
```html
<form id="userForm">
    <input id="id" name="id" th:value="${user.id}"/>
    <input id="username" name="username" th:value="${user.username}"/>
    <input id="password" name="password" th:value="${user.password}"/>
</form>

<div th:text="hello"></div>
<div th:text="${user.username}"></div>
```


#### 选择变量表达式 `*{}`
*使用方法 : 首先通过`th:object`获取对象,然后使用`th:xx = "*{}"`获取对象属性*
```html
<form id="userForm" th:object="${user}">
    <input id="id" name="id" th:value="*{id}"/>
    <input id="username" name="username" th:value="*{username}"/>
    <input id="password" name="password" th:value="*{password}"/>
</form>
```


#### 链接表达式 `@{}`
*使用方法 : 通过链接表达式`@{}`直接拿到应用路径,然后拼接静态资源路径*
```html
<!-- 访问项目路径下的user并传递id,name等参数 -->
<a th:href="@{~/user(id=1,name=demo)}">demo</a> 

<script th:src="@{/webjars/jquery/jquery.js}"></script>
<link th:href="@{/webjars/bootstrap/css/bootstrap.css}" rel="stylesheet" type="text/css">
```


#### 片段表达式 `~{}`
1. *`~{viewName}` : 表示引入完整页面*
2. *`~{viewName ::selector}` : 表示在指定页面寻找片段,其中`selector`可为片段名、jquery选择器等*
3. *`~{::selector}` : 表示在当前页寻找*
   
*使用方法 : 首先通过`th:fragment`定制片段,然后通过`th:replace`填写片段路径和片段名*
```html
<!-- /views/common/head.html-->
<head th:fragment="static">
        <script th:src="@{/webjars/jquery/3.3.1/jquery.js}"></script>
</head>
<!-- /views/your.html -->
<div th:replace="~{common/head::static}"></div>
```
- *注意 : 由于默认拼接的路径为`spring.thymeleaf.prefix = classpath:/templates/`,所以在使用替换路径`th:replace`开头请勿添加斜杠,避免部署运行的时候出现路径报错 !*


#### 消息表达式 `&#35;{}`
*即通常的国际化属性 : `&#35;{msg}`,用于获取国际化语言翻译值*
```html
<title th:text="#{user.title}"></title>
```


#### 其它表达式
*在基础语法中,默认支持字符串连接、数学运算、布尔逻辑和三目运算等..*
```html
<input name="name" th:value="${'I am '+(user.name!=null?user.name:'NoBody')}"/>
```



### 常用语法

#### 迭代循环
*想要遍历List集合很简单,配合`th:each`即可快速完成迭代*
```html
<div th:each="user:${userList}">
    账号: <input th:value="${user.username}"/>
    密码: <input th:value="${user.password}"/>
</div>
```

*在集合的迭代过程还可以获取状态变量,只需在变量后面指定状态变量名即可,状态变量可用于获取集合的下标/序号、总数、是否为单数/偶数行、是否为第一个/最后一个*
```html
<div th:each="user,stat:${userList}" th:class="${stat.even}?'even':'odd'">
    下标: <input th:value="${stat.index}"/>
    序号: <input th:value="${stat.count}"/>
    账号: <input th:value="${user.username}"/>
    密码: <input th:value="${user.password}"/>
</div>
```

*如果缺省状态变量名,则迭代器会默认帮我们生成以变量名开头的状态变量 xxStat*
```html
<div th:each="user:${userList}" th:class="${userStat.even}?'even':'odd'">
    下标：<input th:value="${userStat.index}"/>
    序号：<input th:value="${userStat.count}"/>
    账号：<input th:value="${user.username}"/>
    密码：<input th:value="${user.password}"/>
</div>
```


#### 条件判断

##### if语句
*其语法为 : `th:if`,通常用于动态页面的初始化*
```html
<div th:if="${userList}">
    <div>exist...</div>
</div>
```
*如果想取反则可以使用`unless`*
```html
<div th:unless="${userList}">
    <div>not exist...</div>
</div>
```

##### switch语句
*其语法中需要`th:switch`与`th:case`结合使用,通常用于动态页面的初始化*
```html
<div th:switch="${num}">
    <span th:case="1">num=1</span>
    <span th:case="2">num=2</span>
    <span th:case="3">num=3</span>
</div
```


#### 日期格式化
*使用默认的日期格式(toString方法)并不是我们预期的格式 : `Mon Dec 03 23:16:50 CST 2018`*
```html
<input type="text" th:value="${user.createTime}"/>
```
*此时可以通过时间工具类`&#35;dates`来将日期进行格式化为 : `2018-12-03 23:16:50`*
```html
<input type="text" th:value="${#dates.format(user.createTime,'yyyy-MM-dd HH:mm:ss')}"/>
```


#### 内联写法
1. *内联写法 : 解决 JS无法获取服务端返回的变量的尴尬 ~*
2. *使用方法 : 标准格式为: `[[${xx}]]`,其可以读取服务端变量,也可以调用内置对象的方法.例如获取用户变量和应用路径:*
```html
<script th:inline="javascript">
    var user = [[${user}]];`
    var APP_PATH = [[${#request.getContextPath()}]];
    var LANG_COUNTRY = [[${#locale.getLanguage()+'_'+#locale.getCountry()}]];
</script>
```



### 内置对象

#### 七大基础对象
1. *`${&#35;ctx}` : 上下文对象,可用于获取其它内置对象*
2. *`${&#35;vars}` : 上下文变量*
3. *`${&#35;locale}` : 上下文区域设置*
4. *`${&#35;request}` : HttpServletRequest对象*
5. *`${&#35;response}` : HttpServletResponse对象*
6. *`${&#35;session}` : HttpSession对象*
7. *`${&#35;servletContext}` : ServletContext对象*


#### 常用的工具类
1. *`&#35;strings` :字符串工具类*
2. *`&#35;lists` : List工具类*
3. *`&#35;arrays` : 数组工具类*
4. *`&#35;sets` : Set工具类*
5. *`&#35;maps` : 常用Map方法*
6. *`&#35;objects` : 一般对象类,通常用来判断非空*
7. *`&#35;bools` : 常用的布尔方法*
8. *`&#35;execInfo` : 获取页面模板的处理信息*
9. *`&#35;messages` : 在变量表达式中获取外部消息的方法,与使用`&#35;{...}`语法获取的方法相同*
10. *`&#35;uris` : 转义部分URL / URI的方法*
11. *`&#35;conversions` : 用于执行已配置的转换服务的方法*
12. *`&#35;dates` : 时间操作和时间格式化等*
13. *`&#35;calendars` : 用于更复杂时间的格式化*
14. *`&#35;numbers` : 格式化数字对象的方法*
15. *`&#35;aggregates` : 在数组或集合上创建聚合的方法*
16. *`&#35;ids` : 处理可能重复的id属性的方法*



- *🙂thanks a lot : https://www.jianshu.com/p/908b48b10702*