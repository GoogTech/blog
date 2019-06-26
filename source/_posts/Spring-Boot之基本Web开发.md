---
title: Spring Boot之基本Web开发
date: 2019-06-26 10:11:58
tags: [Spring Boot]
---

## 学习笔记 : Spring Boot之基本Web开发

### @ServletComponentScan的使用
*在SpringBootApplication上使用`@ServletComponentScan`注解后,Servlet、Filter、Listener可以直接通过 @WebServlet、@WebFilter、@WebListener 注解自动注册,无需其他代码哟 !*


#### 开发Servlet
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

    <groupId>io.github.yubuntu0109</groupId>
    <artifactId>web-servlet</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>web-servlet</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>11</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>

</project>
```

2. *MyServlet.java : 自定义Servlet*
```java
package pers.huangyuhui.web.servlet;

import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @project: web-servlet
 * @description: learn to use the annotation: @ServletComponentScan
 * @author: 黄宇辉
 * @date: 6/26/2019-9:49 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@WebServlet(name = "MyServlet", urlPatterns = "/helloServlet")
public class MyServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
        System.out.println("this's my servlet ~");
    }
}
```

3. *WebServletApplication.java : Spring Boot启动类*
```java
package pers.huangyuhui.webservlet;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.ServletComponentScan;

@SpringBootApplication
@ServletComponentScan
public class WebServletApplication {

    public static void main(String[] args) {
        SpringApplication.run(WebServletApplication.class, args);
    }

}
```

4. *程序运行结果*
```
this's my servlet ~
```


#### 开发Filter
1. *MyFilter.java : 自定义过滤器*
```java
package pers.huangyuhui.web.filter;

import javax.servlet.*;
import javax.servlet.annotation.WebFilter;
import java.io.IOException;

/**
 * @project: web-servlet
 * @description: learn to user annotation: @ServletComponentScan
 * @author: 黄宇辉
 * @date: 6/26/2019-10:30 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */

@WebFilter(filterName = "MyFile", urlPatterns = "/helloServlet")
public class MyFilter implements Filter {


    @Override
    public void init(FilterConfig filterConfig) {
    }

    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        System.out.println("this's my filetr !");
        filterChain.doFilter(servletRequest, servletResponse);
    }

    @Override
    public void destroy() {
    }
}
```

2. *程序运行结果*
```
this's my filetr !
this's my servlet ~
```


#### 开发Listener
1. *MyListener.java : 自定义监听器*
```java
package pers.huangyuhui.web.listener;

import javax.servlet.ServletContextEvent;
import javax.servlet.ServletContextListener;
import javax.servlet.annotation.WebListener;

/**
 * @project: web-servlet
 * @description: learn to user annotation: @ServletComponentScan
 * @author: 黄宇辉
 * @date: 6/26/2019-10:54 AM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@WebListener(value = "MyListenre")
public class MyListener implements ServletContextListener {

    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("this's contextInitialized ~");
    }

    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("this's contextDestroyed ~");
    }

}
```

2. *程序运行结果*
```
this's requestInitialized ~
this's my filetr !
this's my servlet ~
this's requestDestroyed ~
```



### 使用Bean注解注册Servlet等组件
*WebServletApplication.java : Spring Boot启动类*
```java
package pers.huangyuhui.web;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletListenerRegistrationBean;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import pers.huangyuhui.web.filter.MyFilter;
import pers.huangyuhui.web.listener.MyListener;
import pers.huangyuhui.web.servlet.MyServlet;

@SpringBootApplication
public class WebServletApplication {

    public static void main(String[] args) {

        SpringApplication.run(WebServletApplication.class, args);
    }

    @Bean
    //使用@Bean注解注册Servlet
    public ServletRegistrationBean getServletRegistration() {
        ServletRegistrationBean servletRegistrationBean = new ServletRegistrationBean(new MyServlet());
        servletRegistrationBean.addUrlMappings("/Bean-helloServlet");
        return servletRegistrationBean;
    }
    /*the result:
        this's my servlet ~
     */

    @Bean
    //使用@Bean注解注册Filter
    public FilterRegistrationBean getFilterRegistrationBean() {
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(new MyFilter());
        filterRegistrationBean.addUrlPatterns("/Bean-helloServlet");
        return filterRegistrationBean;
    }
    /*the result:
        this's my filetr !
        this's my servlet ~
     */

    @Bean
    //使用@Bean注解注册Listener
    public ServletListenerRegistrationBean getServletListenerRegistrationBean() {
        return new ServletListenerRegistrationBean(new MyListener());
    }
     /*the result:
        this's contextInitialized ~
     */

}
```



### 静态资源访问

#### 默认策略
*简介 : Spring Boot默认会过滤所有的静态资源,而静态资源的位置一共有5个,如下所示. 注意: 按照定义的顺序,5个静态资源位置的优先级依次降低! 但是一般情况下,Spring Boot项目不需要`webapp`目录,所以在第5个"`/`"可以暂时不考虑哟 ~*
1. *classpath:/META-INF/resources/*
2. *classpath:/resources/*
3. *classpath:/static/*
4. *classpath:/pulic/*
5. */*
   
- *扩 : 如果你使用的是Intellij IDEA开发工具,记得设置`目录类型`哟 !*
  

#### 自定义策略
*如果默认的静态资源过滤策略不能满足开发需求,也可以自定义资源过滤策略,自定义静态资源过滤策略有如下两种方式 :*
1. *在配置文件中定义*
2. *Java编码定义*

*第一种方式示例 : 可以在`application.properties`中直接定义过滤规则(第一行)和静态资源位置(第二行),示例代码如下 :*
```
spring.mvc.static-path-pattern=/static/**
spring.resources.static-locations=classpath:/static/
```



### 文件的上传
*简介 : 多数文件上传都是通过表单形式提交给服务器的,因此,要实现文件上传功能就需要提供一个上传文件的表单,而此表单必须满足以下三个条件.*
1. *form表单的method的属性设置为post*
2. *form表单的enctype属性设置为multipart/form-data*
3. *提供`<input type="file" name="filename"/>`的文件上传输入框*

*在Spring Boot中可以根据需要在`application.properties`中对上传的文件进行详细的配置,示例代码如下 :*
```
spring.servlet.multipart.enabled=true
spring.servlet.multipart.file-size-threshold=0
spring.servlet.multipart.location=D://upload//photo
spring.servlet.multipart.max-file-size=5MB
spring.servlet.multipart.max-request-size=10MB
spring.servlet.multipart.resolve-lazily=false
```
1. *第一行: 设置是否开启文件上传支持,默认为true*
2. *第二行: 设置文件写入磁盘的阈值,默认为0*
3. *第三行: 指定上传文件的临时保存位置*
4. *第四行: 设置上传的单个文件的最大大小,默认为1MB*
5. *第五行: 设置多文件上传时文件的总大小,默认为10MB*
6. *第六行: 设置文件是否延迟解析,默认为false*


#### 示例程序
*upload.html : 上传文件页面*
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!-- 验证表单信息 -->
    <script type="text/javascript">
        function check() {
            var file = document.getElementById("file").value;
            if (file.length === 0 || file === "") {
                alert("请选择需要上传的文件 !");
                return false;
            }
            return true;
        }
    </script>
</head>
<body>
<form action="../fileUpload" method="post" enctype="multipart/form-data" onsubmit="return check()">
    请选择文件 <input id="file" type="file" name="uploadfile"/><br>
    <input type="submit" value="上传"/>
</form>
</body>
</html>
```

*FileController.java : 操控文件的控制器*
```java
package pers.huangyuhui.web.controller;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;
import java.util.UUID;

/**
 * @project: web-servlet
 * @description: 操控文件的控制器
 * @author: 黄宇辉
 * @date: 6/26/2019-4:34 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@RestController
public class FileController {

    /**
     * @description: 上传文件
     * @param: uploadfile
     * @param: request
     * @date: 2019-06-26 5:00 PM
     * @return: java.lang.String
     */
    @RequestMapping("/fileUpload")
    public String handleFormUpload(MultipartFile uploadfile, HttpServletRequest request) {
        if (!uploadfile.isEmpty() && uploadfile.getSize() > 0) {
            //获取上传文件的原始名称
            final String originFileName = uploadfile.getOriginalFilename();
            //指定上传文件的保存目录
            final String dirPath = request.getServletContext().getRealPath("/upload/photo/");
            //若保存文件的目录不存在,则创建该目录
            File file = new File(dirPath);
            if (!file.exists()) {
                file.mkdirs();
            }
            //使用UUID重命名文件名称(uuid__原始文件名称)
            final String newFileName = UUID.randomUUID() + "__" + originFileName;
            try {
                //将上传的文件保存到目标目录下
                uploadfile.transferTo(new File(dirPath + newFileName));
            } catch (IOException e) {
                e.printStackTrace();
            }
            return "success to upload the file !";
        }
        return "fail to upload the file !";
    }

}
```