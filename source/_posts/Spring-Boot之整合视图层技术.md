---
title: Spring Boot之整合视图层技术
date: 2019-06-26 22:33:47
tags: [Spring Boot]
---

## 学习笔记 : Spring Boot之整合视图层技术
*简介 : 在目前的企业级应用开发中,前后端分离是趋势,但是视图技术还有一席之地. Spring Boot对视图层技术提供了很好的支持,官方推荐使用的模板引擎是`Thymeleaf`,也支持`FreeMarker`哟,需要注意的是Spring Boot官方并不推荐使用`JSP`技术 !*

### 整合Thymeleaf
*`Thymeleaf`是新一代模板引擎,类似于`Velocity`,`FreeMarker`等传统Java模板引擎.与其不同的是: Thymeleaf支持HTML原型,同时Spring Boot也提供了Thymeleaf自动化配置解决方案. 更多资料见 : https://www.thymeleaf.org/*

#### 配置Thymeleaf
*Spring Boot为Thymeleaf提供了自动化配置类`ThymeleafAutoConfiguration`,相关的配置信息在`ThymeleafProperties`类中,其部分源码如下所示 :*
```java
@ConfigurationProperties(
    prefix = "spring.thymeleaf"
)
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";

    ······
```

*如果需要对默认的Thymeleaf配置参数进行自定义,那么可以直接在application.properties中进行配置哟,常见配置如下:*
```
#是否开启缓存,开发时可设置为false,默认为true
spring.thymeleaf.cache=true
#检查模板是否存在,默认为true
spring.thymeleaf.check-template=true
#检查模板位置是否存在,默认为true
spring.thymeleaf.check-template-location=true
#模板文件编码
spring.thymeleaf.encoding=UTF-8
#模板文件位置
spring.thymeleaf.prefix=classpath:/templates/
#Content-Type配置
spring.thymeleaf.servlet.content-type=text/html
#模板文件后缀
spring.thymeleaf.suffix=.html
```


#### 示例程序
1. *pom.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <!-- lookup parent from repository -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.6.RELEASE</version>
        <relativePath/>
    </parent>
    <groupId>pers.huangyuhui</groupId>
    <artifactId>view</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>view</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>11</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```

2. *Book.java : 图书信息*
```java
package pers.huangyuhui.view.bean;

/**
 * @project: view
 * @description: 图书信息
 * @author: 黄宇辉
 * @date: 6/26/2019-9:24 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class Book {

    private Integer id;
    private String name;
    private String author;

    public Book(Integer id, String name, String author) {
        this.id = id;
        this.name = name;
        this.author = author;
    }

    //getter and setter ...
}
```

3. *BookDao.java : 获取并存储图书信息*
```java
package pers.huangyuhui.view.dao;

import pers.huangyuhui.view.bean.Book;

import java.util.ArrayList;
import java.util.List;

/**
 * @project: view
 * @description: 操控图书信息
 * @author: 黄宇辉
 * @date: 6/26/2019-9:41 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
public class BookDao {

    //存储图书信息
    private static List<Book> books = new ArrayList<>();

    public static List<Book> getBooks() {
        Book book1 = new Book(1, "Spring", "spring-author");
        Book book2 = new Book(2, "Spring MVC", "springmvc-author");
        Book book3 = new Book(3, "MyBatis", "mybatis-author");
        Book book4 = new Book(4, "Spring Boot", "springboot-author");
        books.add(book1);
        books.add(book2);
        books.add(book3);
        books.add(book4);
        return books;
    }
}
```

4. *BookController.java : 操控图书信息的控制器*
```java
package pers.huangyuhui.view.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.servlet.ModelAndView;
import pers.huangyuhui.view.dao.BookDao;

/**
 * @project: view
 * @description: 操控图书信息的控制器
 * @author: 黄宇辉
 * @date: 6/26/2019-9:26 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@Controller
public class BookController {

    @GetMapping("/getBookList")
    public ModelAndView books() {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.addObject("books", BookDao.getBooks());
        modelAndView.setViewName("bookList");
        return modelAndView;
    }

}
```

5. *bookList.html : 展示图书列表信息的页面*
```html
<!DOCTYPE html>
<!-- 导入Thymeleaf的名称空间 -->
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>图书信息列表页面-Thymeleaf</title>
</head>
<body>
<table border="1">
    <tr>
        <td>图书编号</td>
        <td>图书名称</td>
        <td>图书作者</td>
    </tr>
    <tr th:each="book:${books}">
        <td th:text="${book.id}"></td>
        <td th:text="${book.name}"></td>
        <td th:text="${book.author}"></td>
    </tr>
</table>
</body>
</html>
```



### 整合FreeMarker
*`FreeMarker`是一个非常古老的模板引擎,可以用在Web环境或者非Web环境中. 与`Thymeleaf`不同的是FreeMarker需要经过解析才能够在浏览器中展示出来. 更多资料见 : https://freemarker.apache.org/*

#### 配置FreeMarker
*Spring Boot对FreeMarker也提供了配置类`FreeMarkerAutoConfiguration`,相关的配置属性在`FreeMarkerProperties`中,其部分源码如下所示 :*
```java
@ConfigurationProperties(
    prefix = "spring.freemarker"
)
public class FreeMarkerProperties extends AbstractTemplateViewResolverProperties {
    public static final String DEFAULT_TEMPLATE_LOADER_PATH = "classpath:/templates/";
    public static final String DEFAULT_PREFIX = "";
    public static final String DEFAULT_SUFFIX = ".ftl";
    private Map<String, String> settings = new HashMap();
    private String[] templateLoaderPath = new String[]{"classpath:/templates/"};
    private boolean preferFileSystemAccess = true;

    ······
```

*如果需要对默认的FreeMarker配置参数进行自定义,那么可以直接在application.properties中进行配置哟,常见配置如下:*
```
#HttpServletRequest的属性是否可以覆盖controller中model的同名项
spring.freemarker.allow-request-override=false
#HttpSession的属性是否可以覆盖controller中的model的同名项
spring.freemarker.allow-session-override=false
#是否开启缓存
spring.freemarker.cache=false
#模板文件编码
spring.freemarker.charset=UTF-8
#是否检查模板位置
spring.freemarker.check-template-location=true
#Content-type的值
spring.freemarker.content-type=text/html
#是否将HttpServletRequest中的属性添加到Model中
spring.freemarker.expose-request-attributes=false
#是否将HttpSession中的属性添加到Model中
spring.freemarker.expose-session-attributes=false
#模板文件后缀
spring.freemarker.suffix=.ftl
#模板文件位置
spring.freemarker.template-loader-path=classpath:/templates/
```


#### 示例程序
1. *Book.java,BookDao.java,BookController.java程序与Thymeleaf示例程序相同,此处略写 ...*

2. *在`pom.xml`中需添加FreeMarker依赖*
```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>
```

3. *bookList.html : 展示图书列表的页面*
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>图书列表信息页面-FreeMarker</title>
</head>
<body>
<table border="1">
    <tr>
        <td>图书编号</td>
        <td>图书名称</td>
        <td>图书作者</td>
    </tr>
    <!-- 若model中的books不为空并且books中有数据,则遍历books集合  -->
    <#if books ??&&(books?size>0)>
        <#list books as book>
            <tr>
                <td>${book.id}</td>
                <td>${book.name}</td>
                <td>${book.author}</td>
            </tr>
        </#list>
    </#if>
</table>
</body>
</html>
```



### 整合JSP
*Spring Boot官方并不推荐使用`JSP`技术! 但是小哥是个念旧的孩纸,来学习一波呗😁 ~*

1. *Book.java,BookDao.java,BookController.java程序与Thymeleaf示例程序相同,此处略写 ...*

2. *在`pom.xml`中需添加使用JSP所需的依赖*
```xml
<!-- JSTL -->
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>jstl</artifactId>
</dependency>
<!-- 用于编译JSP -->
<dependency>
    <groupId>org.apache.tomcat.embed</groupId>
    <artifactId>tomcat-embed-jasper</artifactId>
    <scope>provided</scope>
</dependency>
```

3. *`application.properties`配置文件中用于解析视图的代码*
```
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

4. *bookList.jsp : 展示图书列表信息的页面*
```html
<%@ page contentType="text/html;charset=UTF-8" %>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>图书列表信息页面-JSP</title>
</head>
<body>
<table border="1">
    <tr>
        <td>图书编号</td>
        <td>图书名称</td>
        <td>图书作者</td>
    </tr>
    <c:forEach items="${books}" var="book">
    <tr>
        <td>${book.id}</td>
        <td>${book.name}</td>
        <td>${book.author}</td>
    </tr>
    </c:forEach>
</body>
</html>
```

5. *程序运行结果(以上三个示例程序运行结果大同小异哟)*
![ ](Spring-Boot之整合视图层技术/BookInfoView.PNG)