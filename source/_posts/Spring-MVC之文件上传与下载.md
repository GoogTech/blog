---
title: Spring MVC之文件上传与下载
date: 2019-05-28 21:01:46
tags: [Spring MVC]
---

## 学习笔记 : `Spring MVC`之文件的上传与下载

### 文件上传
*简介 : 多数文件上传都是通过表单形式提交给服务器的,因此,要实现文件长传功能后,就需要提供一个文件上传的表单,而表单必须满足以下三个条件.*
- *form表单的method的属性设置为`post`*
- *form表单的enctype属性设置为`multipart/form-data`*
- *提供`<input type="file" name="filename"/>`的文件上传输入框*


*当客户端form表单的`enctype`属性为`multipart/form-data`时,浏览器就会采用二进制流的方式来处理表单数据,服务器端就会对文件上传的请求进行解析处理. Spring MVC为文件上传提供了支持,这种支持是通过`MultipartResolver`(多部件解析器)对象实现的. MultipartResolver是一个接口对象,需要通过它的实现类`CommonsMultipartResolver`来完成文件的上传工作,在需要在spring mvc配置文件中定义MultipartResolver接口的Bean即可,其配置方式如下 :*

- *springmvc-config.xml*
```xml
<!-- 配置文件上传解析器:MultipartResolver -->
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
    <!-- 设置请求编码格式 -->
    <property name="defaultEncoding" value="UTF-8"/>
    <!-- 设置允许上传文件的最大值(10MB),单位为字节 -->
    <property name="maxUploadSize" value="20971520"/>
</bean>
```
- *注意: 因为`MultipartResolver`接口的实现类`CommonsMultipartResolver`内部是引用`multipartResolver`字符串获取该实现类对象并完成文件解析的,所以在配置`CommonsMultipartResolver`时必须指定该Bean的id为multipartResolver*


#### 文件上传案例
1. *web.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <welcome-file-list>
        <welcome-file>index.jsp</welcome-file>
    </welcome-file-list>

    <!-- 配置Spring MVC前端控制器 -->
    <servlet>
        <servlet-name>spring_mvc</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!-- 指定Spring MVC核心配置文件路径 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-config.xml</param-value>
        </init-param>
        <!-- 使服务器启动后立即加载Spring MVC核心配置文件 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring_mvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```


2. *Springmvc-config.xml : Spring MVC核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!-- 定义组件扫描器,指定需要扫描的包 -->
    <context:component-scan base-package="pers.huangyuhui.file.controller"/>

    <!-- 注解驱动 -->
    <mvc:annotation-driven/>

    <!-- 视图解析器 -->
    <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/view/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    
    <!-- 配置文件上传解析器:MultipartResolver -->
    <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
        <!-- 设置请求编码格式 -->
        <property name="defaultEncoding" value="UTF-8"/>
        <!-- 设置允许上传文件的最大值(10MB),单位为字节 -->
        <property name="maxUploadSize" value="20971520"/>
    </bean>

</beans>
```

1. *upload.jsp : 文件上传页面*
```html
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 6/1/2019
  Time: 4:01 PM
--%>
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head>
    <title>upload file</title>
    <!-- 验证表单信息 -->
    <script type="text/javascript">
        function check() {
            var name = document.getElementById("name").value;
            var file = document.getElementById("file").value;
            if (name === "") {
                alert("请填写上传人 !");
                return false;
            }
            if (file.length === 0 || file === "") {
                alert("请选择上传文件 !");
                return false;
            }
            return true;
        }
    </script>
</head>
<body>
<form action="${pageContext.request.contextPath}/fileUpload"
      method="post" enctype="multipart/form-data" onsubmit="return check()">

    上传人 <input id="name" type="text" name="name" multiple="multiple"/><br>
    请选择文件 <input id="file" type="file" name="uploadfile"/><br>
    <input type="submit" value="上传"/>
</form>
</body>
</html>
```

4. *FileController.java : 文件上传控制器*
```java
package pers.huangyuhui.file.controller;

import org.apache.commons.io.FileUtils;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.nio.charset.StandardCharsets;
import java.util.List;
import java.util.UUID;

/**
 * @project: file
 * @description: 文件控制器
 * @author: HuangYuhui
 * @date: 6/1/2019-5:17 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@Controller
public class FileController {

    /**
     * @description: upload the specified file
     * @param: name 上传人姓名
     * @param: uploadfile 上传文件
     * @param: request
     * @date: 2019-06-08 6:20 PM
     * @return: java.lang.String
     */
    @RequestMapping("/fileUpload")
    public String handleFormUpload(String name, List<MultipartFile> uploadfile, HttpServletRequest request) {
        //判断文件是否存在
        if (!uploadfile.isEmpty() && uploadfile.size() > 0) {
            //遍历文件
            for (MultipartFile files : uploadfile) {
                //获取上传文件的原始名称
                String originFileName = files.getOriginalFilename();
                //设置上传文件的保存地址目录
                String dirPath = request.getServletContext().getRealPath("/upload/");
                System.out.println(dirPath);
                //如果保存文件的地址不存在,则创建该目录
                File file = new File(dirPath);
                if (!file.exists()) {
                    file.mkdirs();
                }
                //使用UUID重新命名上传的文件名称(上传人_uuid_原始文件名称)
                String newFileName = name + "_" + UUID.randomUUID() + "_" + originFileName;

                try {
                    //使用MultipartFile接口的方法将文件上传保存到目标目录下
                    files.transferTo(new File(dirPath + newFileName));
                } catch (IOException e) {
                    e.printStackTrace();
                }

            }
            return "success";
        }
        return "error";
    }

}

```
- *注意: `upload`文件夹是在项目的发布路径中,而非创建的项目所在路径哟 !*



### 文件下载
*简介 : Spring MVC提供了一个`ResponseEntity`类型的对象,使用它可以很方便地定义返回的`HttpHeaders`与`HttpStatus`对象,通过对这两个对象的设置,既可完成下载文件时所需对的配置信息.*

1. *download.jsp : 文件下载页面*
```html
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 6/1/2019
  Time: 7:18 PM
--%>
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ page import="java.net.URLEncoder" %>
<%@ page import="java.nio.charset.StandardCharsets" %>
<html>
<head>
    <title>文件下载</title>
</head>
<body>
<a href="${pageContext.request.contextPath}/fileDownload?filename=<%=URLEncoder.encode("黄宇辉好可耐.jpg",StandardCharsets.UTF_8)%>">
    点击下载图片哟!</a>
</body>
</html>
```

2. *FileController.java : 文件下载控制器*
```java
package pers.huangyuhui.file.controller;

import org.apache.commons.io.FileUtils;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.multipart.MultipartFile;

import javax.servlet.http.HttpServletRequest;
import java.io.File;
import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.nio.charset.StandardCharsets;
import java.util.List;
import java.util.UUID;

/**
 * @project: file
 * @description: 文件操作控制器
 * @author: HuangYuhui
 * @date: 6/1/2019-5:17 PM
 * @version: 1.0
 * @website: https://yubuntu0109.github.io/
 */
@Controller
public class FileController {

    /**
     * @description: download the specified file
     * @param: request
     * @param: filename
     * @date: 2019-06-08 6:23 PM
     * @return: org.springframework.http.ResponseEntity<byte [ ]>
     */
    @RequestMapping("/fileDownload")
    public ResponseEntity<byte[]> fileDownload(HttpServletRequest request, String filename) throws IOException {

        //指定要下载的文件的所在路径
        String path = request.getServletContext().getRealPath("/upload/");
        //创建该文件对象
        File file = new File(path + File.separator + filename);
        //对文件名进行编码,防止中文文件乱码
        filename = this.getFileName(request, filename);
        //设置响应头
        HttpHeaders httpHeaders = new HttpHeaders();
        //通知浏览器以下载的方式打开文件
        httpHeaders.setContentDispositionFormData("attachment", filename);
        //定义以流的形式下载返回文件数据
        //MediaType.APPLICATION_OCTET_STREAM的值为application/octet-stream,既表示以二进制流的形式下载数据
        httpHeaders.setContentType(MediaType.APPLICATION_OCTET_STREAM);
        //使用Spring MVC框架的ResponseEntity对象封装返回的下载数据
        //HttpStatus类型代表Http协议中的状态,HttpStatus.OK表示200,既服务器已成功处理了请求
        return new ResponseEntity<byte[]>(FileUtils.readFileToByteArray(file), httpHeaders, HttpStatus.OK);
    }

    /**
     * @description: 根据浏览器的不同进行编码设置, 返回编码后的文件名
     * @param: request
     * @param: filename
     * @date: 2019-06-08 6:24 PM
     * @return: java.lang.String
     */
    public String getFileName(HttpServletRequest request, String filename) throws UnsupportedEncodingException {

        //IE不同版本的User-Agent中出现的关键字
        String[] IEBrowserkeyWords = {"MSIE", "Trident", "Edge"};
        //获取请求头代理信息
        String userAgent = request.getHeader("User-Agent");
        for (String keyWork : IEBrowserkeyWords) {
            if (userAgent.contains(keyWork)) {
                //IE内核浏览器,统一设置为UTF-8编码显示
                return URLEncoder.encode(filename, StandardCharsets.UTF_8.name());
            }
        }

        //火狐等其它浏览器统一设置为ISO-8859-1编码显示
        return new String(filename.getBytes(StandardCharsets.UTF_8.name()), StandardCharsets.ISO_8859_1.name());
    }

}
```