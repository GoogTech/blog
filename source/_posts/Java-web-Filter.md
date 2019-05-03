---
title: 'Java web:Filter'
date: 2019-05-02 09:29:12
tags: [Java web]
---

## 学习笔记 : Java web之过滤器

### 简介
*`Filter`被称作过滤器或者拦截器,作为Servlet 2.3中新增的技术,可以实现用户在访问某个目标资源之前,对访问的请求和响应进行相应处理.其基本功能就是对Servlet容器调用`Servlet`的过程进行拦截,从而在Servlet响应处理前后实现一些特殊功能. Filter过滤器实现了`javax.servlet.Filter`接口.在Filter接口中定义了三个方法,其代表Filter的生命周期,如下所示 :*
```java
// 用于初始化过滤器
init(FilterConfig filterConfig);
// 客户端请求时被调用(Filter所有的工作都集中在此方法中)
doFilter(ServletRequest request,ServletResponse response,FilterChain chain);
// 在Web应用程序卸载时被调用
destory();
```


### Filter annotation
*简介 : `Servlet3.0`提供`@WebFilter注解`将一个实现了`javax.servlet.Filter`接口的类定义为过滤器,继而当我们在Web应用中使用过滤器时无需在web.xml文件中配置Filter映射 !*

#### 注解示例-拦截用户的所有请求
```java
@WebFilter(filterName="/MyFilter" , urlPatterns= "/*")
public class MyFilter implements Filter {
    ···
}
```

#### 注解示例-拦截不同方式的访问请求
*描述 : 根据被Servlet容器调用的方式来拦截指定的资源.其调用方式如下所示 :*
```java
package javax.servlet;

/**
 * Enumeration of filter dispatcher types.
 *
 * @since Servlet 3.0
 */
public enum DispatcherType {
    FORWARD,
    INCLUDE,
    REQUEST,
    ASYNC,
    ERROR
}
```

*下面以`FORWARD`(如果目标资源是通过`RequestDispatcher`的`forward()`方法访问时,该过滤器将会被调用)为例,演示Filter对转发请求的过滤效果.*

1. *ServletTest : 将请求转发给index.jsp页面.*
```java
@WebServlet("/ServletTest")
public class ServletTest extends HttpServlet {
	private static final long serialVersionUID = 1L;

	public ServletTest() {
		super();
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		request.getRequestDispatcher("/index.jsp").forward(request, response);
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		doGet(request, response);
	}

}
```

2. *FilterTest.java : 拦截index.jsp页面.*
```java
// Servlet 3.0
@WebFilter(filterName = "/FilterTest", urlPatterns = "/index.jsp", dispatcherTypes = { DispatcherType.FORWARD }) 																				
public class FilterTest implements Filter {

	public FilterTest() {
	}

	public void destroy() {
	}

	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		response.getWriter().println("This is Filter !");

	}

	public void init(FilterConfig fConfig) throws ServletException {
	}

}
```


### FilterConfig interface
*简介 : 为了获取Filter程序在`web.xml`中的配置信息,Servlet API提供了一个FilterConfig接口,该接口封装了Filter程序在web.xml中的所有注册信息.并提供了一系列获取这些配置信息的方法.(同时适用于注解的哟 ~)*

#### getInitParameter(String name)
*描述 : 下面以接口FilterConfig接口中的`getInitParameter(String name)`方法为例,演示FilterConfig接口的作用.*

1. *FilterConfigTest.java : 获取注解中指定名称的初始化参数值.*
```java
// 使用注解为该Filter设置初始化参数: initParams= {@WebInitParam(name="encoding",value="utf-8")}
@WebFilter(filterName = "/FilterConfigTest", urlPatterns = "/MyServlet", initParams = {
		@WebInitParam(name = "encoding", value = "utf-8") })
public class FilterConfigTest implements Filter {

	FilterConfig filterConfig;
	String characterEncoding = null;

	public FilterConfigTest() {
	}

	public void destroy() {
	}

	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {

		response.setContentType("text/html;charset=utf-8");

		characterEncoding = filterConfig.getInitParameter("encoding");
		response.getWriter().println("encoding的初始化参数的值为: " + characterEncoding);
	}

	public void init(FilterConfig fConfig) throws ServletException {
		this.filterConfig = fConfig;
	}

}
```

2. *FilterConfigTest.java : 获取web.xml中指定名称的初始化参数值(代码同FilterConfigTest.java),web.xml中的配置信息如下 :*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	id="WebApp_ID" version="4.0">
	<display-name>Filter_basic</display-name>
	<welcome-file-list>
		<welcome-file>index.html</welcome-file>
		<welcome-file>index.htm</welcome-file>
		<welcome-file>index.jsp</welcome-file>
		<welcome-file>default.html</welcome-file>
		<welcome-file>default.htm</welcome-file>
		<welcome-file>default.jsp</welcome-file>
	</welcome-file-list>

	<filter>
		<filter-name>FilterConfigTest</filter-name>
		<filter-class>pers.huangyuhui.filter.filterconfig.FilterConfigTest</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>gbk2312</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>FilterConfigTest</filter-name>
		<url-pattern>/MyServlet</url-pattern>
	</filter-mapping>

</web-app>
```


### Filter application

#### Filter 实现用户自动登录
*案例描述 : 使用Cookie实现用户自动登录后,当客户端访问服务器的Servlet时,所有的Servelt都需要对用户的Cookie信息进行校验,这样势必会导致在Servlet程序中书写大量重复的代码.为了解决这个问题,可以在Filter程序中实现Cookie的校验.由于Filter可以对服务器的所有请求进行拦截,因此,一旦请求通过Filter程序,就相当于用户信息校验通过,然后Servelt程序就可以根据获取到的用户信息实现自动登录功能了.*

1. *User.java : 封装用户的信息.*
```java
package pers.huangyuhui.filter.example.autologin;

/**
 * @ClassName: User
 * @Description: 封装用户的信息
 * @author: HuangYuhui
 * @date: May 1, 2019 7:24:26 PM
 * 
 */
public class User {

	private String username;
	private String password;

	public User(String username, String password) {
		super();
		this.username = username;
		this.password = password;
	}

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

}
```

2. *login.jsp : 用户登录页面.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>应用案例 : Filter实现用户自动登录</title>
	</head>
	<body>
		<h3 align="center">用户登录页面</h3>
		<hr/>
			<form action="LoginServlet" method="post">
				<table width="380px" cellpadding="0" align="center">
					 <tr>
					 	<td height="30" align="center">用户名</td>
					 	<td><input type="text" name="userName"/></td>
					 </tr>
					 <tr>
					 	<td height="30" align="center">密&nbsp;码</td>
					 	<td><input type="password" name="userPassword"/></td>
					 </tr>
					 <tr>
					 	<td height="40" align="center">自动登录时间</td>
					 	<td>
					 		<input type="radio" name="autologin" value="${60*60 }"/>一小时
					 		<input type="radio" name="autologin" value="${60*60*24 }"/>一天
					 		<input type="radio" name="autologin" value="${60*60*24*7 }"/>一周
					 		<input type="radio" name="autologin" value="${60*60*24*31 }"/>一个月
					 	</td>
					 </tr>
					 <tr>
					 	<td height="50" colspan="2" align="center">
					 		<input type="submit" value="登录"/>&nbsp;&nbsp;&nbsp;&nbsp;
					 		<input type="reset" value="重置"/>
					 		<span style="font-size:13px;color:red">${errorMsg }</span>
					 	</td>
					 </tr>
				</table>
			</form>
	</body>
</html>
```

3. *LoginServlet.java : 处理用户的登录请求.*
```java
package pers.huangyuhui.filter.example.autologin;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @ClassName: LoginServlet
 * @Description: 处理用户的登录请求
 * @author: HuangYuhui
 * @date: May 1, 2019 9:38:38 PM
 *
 */
@WebServlet("/LoginServlet")
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

	public LoginServlet() {
		super();
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		String name = request.getParameter("userName");
		String password = request.getParameter("userPassword");
		String autoLogin = request.getParameter("autologin");

		// 检查用户登录信息
		if ("YUbuntu0109".equals(name) && "demo".equals(password)) {

			// 将用户对象存入session域
			User user = new User(name, password);
			request.getSession().setAttribute("user", user);

			if (autoLogin != null) {
				Cookie cookie = new Cookie("autologin", name + "-" + password);// 加密
				cookie.setMaxAge(Integer.parseInt(autoLogin));
				cookie.setPath(request.getContextPath());
				response.addCookie(cookie);// 发送自动登录的cookie
			}
			response.sendRedirect("index.jsp"); // 跳转到首页

		} else {
			request.setAttribute("errorMsg", "提示: 用户名或密码错误 !");
			request.getRequestDispatcher("/login.jsp").forward(request, response);
		}
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		doGet(request, response);
	}

}
```

4. *LoginServlet.java : 注销用户的登录信息.*
```java
package pers.huangyuhui.filter.example.autologin;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @ClassName: LogoutServlet
 * @Description: 注销用户登录的信息
 * @author: HuangYuhui
 * @date: May 1, 2019 9:47:49 PM
 *
 */
@WebServlet("/LogoutServlet")
public class LogoutServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

	public LogoutServlet() {
		super();
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		// 将Session会话中保存的User对象删除
		request.getSession().removeAttribute("user");

		// 将自动登录的Cookie删除
		Cookie cookie = new Cookie("autologin", "msg");
		cookie.setPath(request.getContextPath());
		cookie.setMaxAge(0);
		response.addCookie(cookie);
		response.sendRedirect(request.getContextPath() + "/index.jsp");
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		doGet(request, response);
	}

}
```

5. *AutoLoginFilter.java : 拦截用户登录的访问请求.*
```java
package pers.huangyuhui.filter.example.autologin;

import java.io.IOException;

import javax.servlet.Filter;
import javax.servlet.FilterChain;
import javax.servlet.FilterConfig;
import javax.servlet.ServletException;
import javax.servlet.ServletRequest;
import javax.servlet.ServletResponse;
import javax.servlet.annotation.WebFilter;
import javax.servlet.http.Cookie;
import javax.servlet.http.HttpServletRequest;

/**
 * @ClassName: AutoLoginFilter
 * @Description: 拦截用户登录的访问请求
 * @author: HuangYuhui
 * @date: May 1, 2019 9:51:23 PM
 *
 */
@WebFilter(filterName = "/AutoLoginFilter", urlPatterns = "/*")
public class AutoLoginFilter implements Filter {

	public AutoLoginFilter() {
	}

	public void destroy() {
	}

	public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {

		HttpServletRequest request2 = (HttpServletRequest) request;
		Cookie[] cookies = request2.getCookies();

		// 获取一个名为`autologin`的cookie
		String autoLogin = null;
		for (int i = 0; cookies != null && i < cookies.length; i++) {
			if ("autologin".equals(cookies[i].getName())) {
				autoLogin = cookies[i].getValue();
				break;
			}
		}

        // 验证用户登录信息
		if (autoLogin != null) {
			String[] parts = autoLogin.split("-");
			String name = parts[0];
			String password = parts[1];
			if ("YUbuntu0109".equals(name) && "demo".equals(password)) {
                // 将用户的登录信息封装到User对象并存入Sesssion域中,既而完成自动登录
				User user = new User(name, password);
				request2.getSession().setAttribute("user", user);
			}
		}

		chain.doFilter(request, response);
	}

	public void init(FilterConfig fConfig) throws ServletException {
	}

}
```
