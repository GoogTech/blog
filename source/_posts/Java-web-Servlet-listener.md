---
title: 'Java web:Servlet listener'
date: 2019-05-02 09:31:43
tags: [Java web]
---

## 学习笔记 : Java web之Servlet事件监听器

### 概述
*`Servlet`事件监听器可以监听`ServletContext`,`HttpSession`,`ServletRequest`等域对象的创建和销毁过程,监听这些域对象属性的修改以及感知绑定到HttpSession域中某个对象的状态.根据监听事件的不同可以将其分为三类,具体如下 :*

1. *用于监听域对象创建和销毁的事件监听器 : ServletContextListener,HttpSessionListener,ServletRequestListener interface*
2. *用于监听域对象属性增加和删除的事件监听器 : ServletContextAttributeListener,HttpSessionAttributeListener,ServletRequestAttributeListener interface*
3. *用于监听绑定到HttpSession域中某个对象状态的事件监听器 : HttpSessionBindingListener,HttpSessionActivationListener interface*



### 监听域对象的生命周期
*简介 : 在Web应用程序的运行期间,Web容器会创建和销毁三个比较的重要的对象: `ServletContext`,`HttpSession`,`ServletRequest`,这些对象被称为域对象,为了监听这些域对象的生命周期,Servlet API中提供了三个对应的接口: ServletContextListener,HttpSessionListener,ServletRequestListener.*

#### 案例-监听域对象的生命周期
*示例代码如下所示*
```java
/**
 * @ClassName: MyServletListener
 * @Description: 监听域对象的生命周期
 * @author: HuangYuhui
 * @date: May 3, 2019 3:17:56 PM
 * 
 */
@WebListener(value="/MyServletListener")//Servlet 3.0
public class MyServletListener implements ServletContextListener, ServletRequestListener, HttpSessionListener {

	@Override
	public void contextInitialized(ServletContextEvent sce) {
		System.out.println("ServletContext object be initialized !");
	}

	@Override
	public void contextDestroyed(ServletContextEvent sce) {
		System.out.println("ServletContext object be destroyed !");
	}

	@Override
	public void requestInitialized(ServletRequestEvent sre) {
		System.out.println("ServletRequest object be initialized !");
	}

	@Override
	public void requestDestroyed(ServletRequestEvent sre) {
		System.out.println("ServletRequest object be destroyed !");
	}

	@Override
	public void sessionCreated(HttpSessionEvent se) {
		System.out.println("HttpSession object be initialized !");
	}

	@Override
	public void sessionDestroyed(HttpSessionEvent se) {
		System.out.println("HttpSession object be destroyed !");
	}

}
```
*为了查看HttpSessionListener和ServletRequestListener监听器的运行效果,可以新建一个index.jsp页面,当运行Tomcat服务器第一次访问index.jsp时,Web容器除了为这次请求创建了`ServletRequest`对象外,还创建了与这个浏览器对应的`HttpSession`对象,当这两个对象在被创建时,Web容器会调用监听器MyServeltListener中相应的方法,当Web服务器完成这次请求后,`ServletRequest对象会随之销毁.`需要注意的是: 如果刷新index.jsp页面,会再次触发`ServletRequestListener`监听器接口中的方法,继而创建新的ServletRequest对象,但不会创建新的HttpSession对象,这是因为`Web容器会为每次访问请求创建一个新的ServletRequest对象,而对于同一个浏览器在会话期间的后续访问是不会再创建新的HttpSession对象的.`*

#### 案例-统计当前在线人数
*案例描述 : 通常情况下,当一个用户在进入某个网站时服务器就会为其创建一个HttpSessionListener对象,当用户离开网站时HttpSessionListener对象就会别销毁.因此,可以使用实现了`HttpSessionListener`接口的监听器来统计网站的在线人数.*

1. *login.jsp : 用于显示当前在线人数.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>HttpSessionListener案例: 统计当前在线人数</title>
	</head>
	<body>
		<h3 align="center">当前在线人数为: <%=application.getAttribute("count")%></h3>
		<a href="<%=response.encodeURL("logout.jsp")%>">退出登录</a>
	</body>
</html>
```

2. *CountListener.java : 用于统计当前在线人数.*
```java
/**
 * @ClassName: CountListener
 * @Description: 统计在线人数
 * @author: HuangYuhui
 * @date: May 3, 2019 3:40:59 PM
 * 
 */
@WebListener(value = "/CountListener")
public class CountListener implements HttpSessionListener {

	private int count = 0;// 在线人数

	@Override
	// Session对象创建时count++
	public void sessionCreated(HttpSessionEvent se) {
		count++;
		ServletContext servletContext = se.getSession().getServletContext();
		servletContext.setAttribute("count", Integer.valueOf(count));
	}

	@Override
	// Session对象销毁时count--
	public void sessionDestroyed(HttpSessionEvent se) {
		count--;
		ServletContext servletContext = se.getSession().getServletContext();
		servletContext.setAttribute("count", Integer.valueOf(count));
	}

}
```

3. *logout.jsp : 用于注销用户登录.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>HttpSessionListener案例: 统计当前在线人数</title>
	</head>
	<body>
		<%
			session.invalidate();// 使当前的Session失效
		%>
		<h3 align="center">你已退出本系统</h3>
	</body>
</html>
```


### 监听域对象中的属性变更
*简介 : Servlet API 中提供了三个用于监听某个域对象属性变更的接口: `ServletContextAttributelistener`,`HttpSessionAttributerListener`和`ServletRequestAttributeListener`.这三个接口都定义了相同名称的方法,分别用于处理被监听对象属性的增加,删除和替换.*

#### 案例-监听域对象的属性变更
1. *attribute.jsp : 设置各个域对象的属性.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>案例:监听域对象的属性变更</title>
	</head>
	<body>
		<h3 align="center">测试域对象属性信息监听器的页面</h3>
		<%
			getServletContext().setAttribute("name", "YUbuntu0109");
			getServletContext().setAttribute("name", "https://YUbuntu0109.github.io");
			getServletContext().removeAttribute("name");
			
			session.setAttribute("name", "YUbuntu0109");
			session.setAttribute("name","https://YUbuntu0109.github.io");
			session.removeAttribute("name");
			
			request.setAttribute("name", "YUbuntu0109");
			request.setAttribute("name", "https://YUbuntu0109.github.io");
			request.removeAttribute("name");		
		%>
	</body>
</html>
```
   
1. *MyAttributeListener.java : 用于监听各个域对象属性的变更.*
```java
/**
 * @ClassName: MyAttributeListener
 * @Description: 监听域对象的属性变更
 * @author: HuangYuhui
 * @date: May 3, 2019 4:18:40 PM
 * 
 */

@WebListener("/MyAttributeListener")
public class MyAttributeListener
		implements ServletContextAttributeListener, HttpSessionAttributeListener, ServletRequestAttributeListener {

	@Override
	public void attributeAdded(ServletContextAttributeEvent event) {
		System.out.println("ServletContext 添加属性: name=" + event.getServletContext().getAttribute(event.getName()));
	}

	@Override
	public void attributeReplaced(ServletContextAttributeEvent event) {
		System.out.println("ServletContext 替换属性: name=" + event.getServletContext().getAttribute(event.getName()));
	}

	@Override
	public void attributeRemoved(ServletContextAttributeEvent event) {
		System.out.println("ServletContext 移除属性: " + event.getName());
	}

	@Override
	public void attributeAdded(ServletRequestAttributeEvent srae) {
		System.out.println("HttpRequest 添加属性: name=" + srae.getServletRequest().getAttribute(srae.getName()));
	}

	@Override
	public void attributeReplaced(ServletRequestAttributeEvent srae) {
		System.out.println("HttpRequest 替换属性: name=" + srae.getServletRequest().getAttribute(srae.getName()));
	}

	@Override
	public void attributeRemoved(ServletRequestAttributeEvent srae) {
		System.out.println("HttpRequest 移除属性: " + srae.getName());
	}

	@Override
	public void attributeAdded(HttpSessionBindingEvent event) {
		System.out.println("HttpSession 添加属性: name=" + event.getSession().getAttribute(event.getName()));
	}

	@Override
	public void attributeReplaced(HttpSessionBindingEvent event) {
		System.out.println("HttpSession 替换属性: name=" + event.getSession().getAttribute(event.getName()));
	}

	@Override
	public void attributeRemoved(HttpSessionBindingEvent event) {
		System.out.println("HttpSession 移除属性: " + event.getName());
	}
}
```

3. *程序运行结果如下所示.*
```
ServletContext 添加属性: name=YUbuntu0109
ServletContext 替换属性: name=https://YUbuntu0109.github.io
ServletContext 移除属性: name
HttpSession 添加属性: name=YUbuntu0109
HttpSession 替换属性: name=https://YUbuntu0109.github.io
HttpSession 移除属性: name
HttpRequest 添加属性: name=YUbuntu0109
HttpRequest 替换属性: name=https://YUbuntu0109.github.io
HttpRequest 移除属性: name
```

#### 案例-统计当前在线人数名单
*案例描述 : 在上述`统计当前在线人数`的案例中,实际上统计的是Web应用当前的所有会话的数目.它无法统计所有所有在线用户的具体名单! 下面我们通过使用`HttpSessionBindingListener`接口不仅可以统计在线用户的数量,还可以统计在线用户的名单.*

1. *login.jsp : 用户登录页面.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>案例:统计登录用户的在线名单</title>
	</head>
	<body>
		<h3 align="center">用户登录页面</h3>
		<hr/>
			<form action="LoginServlet" method="post">
				<table width="280px" cellpadding="0" align="center">
					 <tr>
					 	<td height="30" align="center">用户名</td>
					 	<td><input type="text" name="userName"/></td>
					 </tr>
					 <tr>
					 	<td height="30" align="center">密&nbsp;码</td>
					 	<td><input type="password" name="userPassword"/></td>
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

2. *User.java : 封装用户信息.*
```java
package pers.huangyuhui.servletlistener.example.onlinenumber2;

import javax.servlet.http.HttpSessionBindingEvent;
import javax.servlet.http.HttpSessionBindingListener;

/**
 * @ClassName: User
 * @Description: 封装用户信息
 * @author: HuangYuhui
 * @date: May 4, 2019 9:10:47 AM
 * 
 */
public class User implements HttpSessionBindingListener {

	private String id;
	private String username;
	private String password;

	public User(String id, String name, String password) {
		this.id = id;
		this.username = name;
		this.password = password;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
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

	@Override
	public void valueBound(HttpSessionBindingEvent event) {
		// System.out.println("将新用户添加到在线名单 : " + event.getName() + "=" + event.getValue());
		OnlineUser.getInstance().addUser(this);
	}

	@Override
	public void valueUnbound(HttpSessionBindingEvent event) {
		// System.out.println("从在线名单中移除该用户 : " + event.getName() + "=" + event.getValue());
		OnlineUser.getInstance().removeUser(this);
	}

}
```

3. *OnlineUser.java : 用于存储和获取在线用户列表.*
```java
package pers.huangyuhui.servletlistener.example.onlinenumber2;

import java.util.HashMap;
import java.util.Map;

/**
 * @ClassName: OnlineUser
 * @Description: 存储及获取在线用户列表
 * @author: HuangYuhui
 * @date: May 4, 2019 9:14:09 AM
 * 
 */
public class OnlineUser {

	private OnlineUser() {

	}

	private static OnlineUser instance = new OnlineUser();

	public static OnlineUser getInstance() {
		return instance;
	}

	private Map<String, String> userMap = new HashMap<>();

	// 将用户添加至在线列表
	public void addUser(User user) {
		userMap.put(user.getId(), user.getUsername());

	}

	// 将用户从在线列表中移除
	public void removeUser(User user) {
		userMap.remove(user.getId());
	}

	// 返回在线的用户列表
	public Map<String, String> getOnlineUsers() {
		return userMap;
	}
}
```

4. *LoginServlet.java : 处理用户登录请求.*
```java
package pers.huangyuhui.servletlistener.example.onlinenumber2;

import java.io.IOException;
import java.util.Map;
import java.util.UUID;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @ClassName: LoginServlet
 * @Description: 处理用户登录请求
 * @author: HuangYuhui
 * @date: May 4, 2019 9:30:23 AM
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

		request.setCharacterEncoding("utf-8");
		String username = request.getParameter("userName");
		String password = request.getParameter("userPassword");

		if (username != null && !username.trim().equals("")) {
			User user = new User(UUID.randomUUID().toString(), username, password);
			request.getSession().setAttribute("user", user);

			Map<String, String> onlineUsers = OnlineUser.getInstance().getOnlineUsers();
			request.setAttribute("onlineUsers", onlineUsers);

			request.getRequestDispatcher("/showOnlineUsers.jsp").forward(request, response);

		} else {

			request.setAttribute("errorMsg", "用户名不能为空 !");
			request.getRequestDispatcher("/login.jsp").forward(request, response);
		}
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		doGet(request, response);
	}

}
```

5. *LogoutServlet.java : 注销用户登录信息.*
```java
package pers.huangyuhui.servletlistener.example.onlinenumber2;

import java.io.IOException;
import java.util.Map;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @ClassName: LogoutServlet
 * @Description: 注销用户登录信息
 * @author: HuangYuhui
 * @date: May 4, 2019 9:54:27 AM
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

		request.getSession().removeAttribute("user");

		Map<String, String> onlineUsers = OnlineUser.getInstance().getOnlineUsers();
		request.setAttribute("onlineUsers", onlineUsers);
		request.getRequestDispatcher("/showOnlineUsers.jsp").forward(request, response);
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		doGet(request, response);
	}

}
```

6. *showOnlineUsers.jsp : 显示所有用户的登录信息以及当前登录的用户.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>应用案例:统计登录用户的在线名单</title>
	</head>
	<body>
		<h3 align="center">用户在线名单如下所示</h3>
			<c:choose>
				<c:when test="${sessionScope.user == null }">
					<a href="${pageContext.request.contextPath }/login.jsp">登录</a><br>
				</c:when>
				<c:otherwise>
					你已成功登录! 欢迎你: ${sessionScope.user.username }
				 	<a href="${pageContext.request.contextPath }/LogoutServlet">退出</a>
				</c:otherwise>
			</c:choose>
		<hr/>
			<c:forEach var="user" items="${requestScope.onlineUsers }">
				<p align='center'>用户名: ${user.value}</p>
			</c:forEach>
	</body>
</html>
```

7. *程序运行效果示例图.*
![ ](Java-web-Servlet-listener/Servlet事件监听器案例-统计当前在线人数名单.PNG)


### 感知被HttpSession绑定的事件监听器
*暂略写 ..*