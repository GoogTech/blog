---
title: Spring MVC的核心类和注解
date: 2019-05-22 21:22:50
tags: [Spring MVC]
---

## 学习笔记 : Spring MVC核心类与注解

### Controller 注解类型
*简介 : `org.springframework.stereotype.Controller`注解类型用于指示Spring类的实例是一个控制器,其注解形式为`@Controller`,使用该注解时只需要通过Spring的扫描机制找到标注了该注解的控制器即可,其操作如下 :*
1. 在配置文件的声明中引入`spring-context`.
2. 使用`<context:component-scan>`元素指定需要扫描的类包.



### RequestMapping 注解类型
*简介 : Spring通过`@Controller`注解找到相应的控制器类后,还需要知道控制器内部对每一个请求是如何处理的,这时就需要使用`org.springframework.web.bind.annotation.RequestMapping`注解类型,该注解类型用于映射一个请求或一个方法,其注解形式为`@RequestMapping`,可以使用该注解标注在一个类或方法上.*
1. 标注在方法上 : 该方法将成为一个请求处理方法,它会在程序接收到对应的URL请求时被调用.
2. 标注在类上 : 该类中的所有方法都将映射为相对于类级别的请求,表示该控制器所处理的所有请求都被映射到value属性值所指的路径下.



### ViewResolver 视图解析器
*简介 : Spring MVC中视图解析器负责解析视图,可以通过在配置文件中定义一个ViewResolver来配置视图解析器.其配置如下 :*
```xml
<!-- 定义视图解析器 -->
<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
	<!-- 设置前缀 -->
	<property name="prefix" value="/WEB-INF/view/"/>
	<!-- 设置后缀 -->
	<property name="suffix" value=".jsp"/>
</bean>
```



### 应用案例
1. *web.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	id="WebApp_ID" version="4.0">
	<display-name>springmvc_core</display-name>
	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>

	<servlet>
		<!-- 设置前端过滤器 -->
		<servlet-name>spring_mvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<!-- 初始化时加载配置文件 -->
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:springmvc-config.xml</param-value>
		</init-param>
		<!-- 容器启动时立即加载Servlet -->
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>spring_mvc</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

</web-app>
```

2. *springmvc-config.xml : Spring MVC配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

	<!-- 指定需要扫描的包 -->
	<context:component-scan base-package="pers.huangyuhui.springmvc.controller"/>
	
	<!-- 定义视图解析器 -->
	<bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<!-- 设置前缀 -->
		<property name="prefix" value="/WEB-INF/view/"/>
		<!-- 设置后缀 -->
		<property name="suffix" value=".jsp"/>
	</bean>

</beans>
```

3. *MyController.java : 控制器*
```java
package pers.huangyuhui.springmvc.controller;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

/**
 * @ClassName: FirstController
 * @Description: 控制器
 * @author: HuangYuhui
 * @date: May 23, 2019 7:16:18 PM
 *
 */
@Controller // 标注为控制器类
@RequestMapping(value = "/springmvc") // 映射请求方法
public class MyController {

	@RequestMapping(value = "/myController")
	public String handleRequest(HttpServletRequest request, HttpServletResponse response, Model model) {

		// 向模型对象中添加数据
		model.addAttribute("msg", "Hi Spring MVC ~");

		// 返回视图页面
		return "hi-springmvc";
	}

}
```

4. *在WebContent/WEB-INF/目录下新建view文件,并在其中新建`hi-springmvc.jsp`.*

5. *将项目发布到Tomcat服务器并启动,在浏览器中访问: `http://localhost:8080/springmvc_core/springmvc/myController` 即可发现页面显示运行结果 : `Hi Sping MVC ~`*