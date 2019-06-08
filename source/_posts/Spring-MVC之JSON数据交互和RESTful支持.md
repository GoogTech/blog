---
title: Spring MVC之JSON数据交互
date: 2019-05-28 21:03:53
tags: [Spring MVC]
---

## 学习笔记 : `Spring MVC`之JSON数据交互


### JSON 数据交互
*简介 : `JSON`(JavaScript Object Notation,JS对象标记) 是一种轻量级的数据交换格式. 它基于JavaScript的一个子集,使用了C,C++,C#,Java,JavaScript,Perl,Python等其它语言的约定,采用完全独立于编程语言的`文本格式来存储和表示数据`. 这些特性使JSON称为理性的数据交互语言 !*


#### JSON 数据转换
*简介 : 为了实现浏览器与控制器`Controller`之间的数据交互,Spring提供了一个`HttpMessageConverter<T>`接口来完成此项工作. 该接口主要用于将请求消息中的数据转换为一个`T`的对象,`并将类型为T的对象绑定到请求方法的参数中,或者将对象转换为响应消息传递给浏览器显示.` Spring为`HttpMessageConverter<T>`接口提供了很多实现类,这些实现类可以对不同的类型的数据进行信息转换,其中`MappingJackson2HttpMessageConverter`是Spring MVC默认处理JSON格式请求响应的实现类. 该实现类利用`Jackson`开源包读写JSON数据,`将Java对象转换为JSON对象和XML文档,同时也可以将JSON对象和XML文档转换为Java对象 !`*


#### 示例程序
1. *web.xml : 对Spring mvc的前端控制器等信息进行配置*
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
        <!-- 指定Spring MVC配置文件的路径 -->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc-config.xml</param-value>
        </init-param>
        <!-- 服务器启动后立即加载Spring MVC配置文件 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>spring_mvc</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

2. *springmvc-config.xml : Spring mvc配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

    <!-- 定义Spring组件扫描器,指定需要扫描的包 -->
    <context:component-scan base-package="pers.huangyuhui.springmvc.controller"/>

    <!--
    配置注解驱动
    该配置会自动注册RequestMappingHandlerMapping和RequestMappingHandlerAdapter两个Bean,
    并提供对读写XML和读写JSON等功能的支持.
    -->
    <mvc:annotation-driven/>

    <!-- 配置静态资源的访问路径,此配置中的文件将不被前端控制器所拦截(该目录存放jquery-1.12.4.js) -->
    <mvc:resources mapping="/js/" location="/js/**"/>

    <!-- 配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/view/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

3. *User.java : 用户信息*
```java
package pers.huangyuhui.springmvc.bean;

/**
 * @project: springmvc_json
 * @package: pers.huangyuhui.springmvc.bean
 * @description: User information
 * @author: HuangYuhui
 * @date: 5/31/2019-1:44 PM
 * @version: 1.0
 */
public class User {

    private String username;
    private String password;

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    @Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", password='" + password + '\'' +
                '}';
    }
}
```

4. *index.jsp : 用于测试JSON交互的页面*
```html
<%--
  Created by IntelliJ IDEA.
  User: Administrator
  Date: 5/31/2019
  Time: 1:46 PM
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
    <title>Spring MVC</title>
    <script src="js/jquery-1.12.4.js"></script>
    <script type="text/javascript">
        function testJson() {
            //用户表单信息
            var username = $("#username").val();
            var password = $("#password").val();
            $.ajax({
                url: "${pageContext.request.contextPath}/testJson",
                type: "post",
                //data:发送的数据
                data: JSON.stringify({username: username, password: password}),
                //定义发送请求的数据格式为JSON字符串
                contentType: "application/json;charset=UTF-8",
                //定义回调响应的数据格式为JSON字符串
                dataType: "json",
                //成功响应的结果
                success: function (data) {
                    if (data != null) {
                        alert("你输入的用户名:" + username + "\n密码:" + password);
                    }
                }
            });
        }
    </script>
</head>
<body>
<form>
    用户名 <input type="text" name="username" id="username"/><br>
    密码<input type="password" name="password" id="password"><br>
    <input type="button" value="Test" onclick="testJson()"/>
</form>
</body>
</html>
```

5. *UserController.java : 控制器*
```java
package pers.huangyuhui.springmvc.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;
import pers.huangyuhui.springmvc.bean.User;

/**
 * @project: springmvc_json
 * @package: pers.huangyuhui.springmvc.controller
 * @description: Controller
 * @author: HuangYuhui
 * @date: 5/31/2019-1:39 PM
 * @version: 1.0
 */
@Controller
public class MyController {

    /**
     * @description: 接收页面请求的JSON数据, 并返回JSON格式结果
     * @param: user
     * @date: 2019-06-08 5:34 PM
     * @return: pers.huangyuhui.springmvc.bean.User
     */
    //@ResponseBody 用于直接返回retrun对象(将Java对象转换为JSON格式的响应数据)
    //@RequestBody 用于将请求体中的数据绑定到方法的形参上(将JSON格式的请求数据转换为Java对象)
    @RequestMapping("/testJson")
    @ResponseBody
    public User testJson(@RequestBody User user) {
        //输出接收的JSON格式数据
        System.out.println(user);
        //返回JSON格式的响应
        return user;
    }

}
```

6. *由程序运行的结果(略..)可知,`JSON`格式的请求数据成功转换为方法中的Java对象,`Java`对象也成功地转换为了JSON格式的响应数据.*



### RESTful
*(っ•̀ω•́)っ✎⁾⁾ 略写...(会单独写一个关于`RESTful`风格的编程笔记哟 ~)*