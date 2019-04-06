---
title: Java Servlet各版本使用详解
date: 2019-04-05 15:22:46
tags: [Java,Servlet]
---

## 详细讲解各版本Java Servlet的使用,这次哥哥讲细节 (✪ω✪) !

### `Servlet 2.5` : 通过配置`web.xml`文件来映射`Servlet` ( 示例代码如下 )

- *Servlet program*
```java
public class FirstServlet extends HttpServlet {

	private static final long serialVersionUID = -7228095681625205247L;

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		System.out.println("This is doGet ..");
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		this.doGet(req, resp);
		System.out.println("This is doPost ..");
	}
}
/*
 * #(doGet)The result be shown as followed : 
 * 
 * Apr 05, 2019 10:15:33 AM org.apache.catalina.core.StandardContext reload INFO: Reloading Context with
 * name [/Servlet_basic] is completed
 * This is doGet ..
 */

/*
 * #(doPost)The result be shown as followed :
 * 
 * Apr 05, 2019 10:57:08 AM org.apache.catalina.core.StandardContext reload INFO: Reloading Context with
 * name [/Servlet_basic] is completed 
 * This is doPost ..
 * 
 */

/*
 * #(doPost —> doGet)The result be shown as followed : 
 * 
 * Apr 05, 2019 11:22:21 AM org.apache.catalina.core.StandardContext reload NFO: Reloading Context with 
 * name [/Servlet_basic] is completed
 * This is doGet ..
 * This is doPost ..
 * 
 */

```

- *JSP Progarm*
```html
<%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="ISO-8859-1">
		<title>Servlet 2.5</title>
	</head>
	<body>
		<!--
        href same as : http://localhost:8080/Servlet_basic_version_2.5/FirstServlet 

	 	注意: 构建路径及`WebContent`都属于 : 根目录.
	 	JSP中的'/'表示服务器的跟路径.如 : http://localhost:8080/
	 	web.xml中的'/'表示项目的根路径.如 : http://localhost:8080/Servlet_basic_version_2.5/
        -->
		<a href="/Servlet_basic_version_2.5/FirstServlet">doGet : FirstServlet</a>
		<!-- 注意 : `form`的默认提交方式为`get`,所以修改为: method="post" -->
		<div>
			<form action="FirstServlet" method="get"><hr>
				<input type="submit" value="doGet " />
			</form>
			<form action="FirstServlet" method="post"><br>
				<input type="submit" value="doPost" />
			</form>
		</div>
	</body>
</html>
```

- *web.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	id="WebApp_ID" version="4.0">
	<display-name>Servlet_basic</display-name>
	<welcome-file-list>
		<welcome-file>index.html</welcome-file>
		<welcome-file>index.htm</welcome-file>
		<welcome-file>index.jsp</welcome-file>
		<welcome-file>default.html</welcome-file>
		<welcome-file>default.htm</welcome-file>
		<welcome-file>default.jsp</welcome-file>
	</welcome-file-list>
	<!-- 
	当程序运行后,点击`HiServlet.jsp`页面中的`url`请求时`web.xml`配置文件中的映射关系如下:

        1.<servlet-mapping> 
                ↓
                ↓
        2.请求被根目录(src/WebContent是根目录)下<url-pattern>标签中的Servlet程序拦截.
                ↓
                ↓ 
        3.(寻找Servlet.class的桥梁) 匹配与其<servlet-name>名称相同的<servlet>.
                ↓
                ↓
        4.通过<servlet-class>标签中的`Servlet.class`响应用户的请求.
               
    可此得知标签<servlet-name>是<servlet>和<servlet-mapping>之间沟通的桥梁( 其名称任意,但其两者间的名称必须相同 ! )
	如不同`Tomcat`则会抛出如下异常信息:
    error..Caused by: java.lang.IllegalArgumentException: Servlet mapping specifies an unknown servlet name [sameName]..
	-->
	<servlet>
		<servlet-name>sameName</servlet-name>
		<servlet-class>pers.huangyuhui.servlet.test.FirstServlet</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>sameName</servlet-name>
		<url-pattern>/FirstServlet</url-pattern>
	</servlet-mapping>
</web-app>
```



### `Servlet 3.0` : 通过`注解`来映射相应的`Servlet`.

- *Servlet program*
```java
/*
1.Servlet 3.0 与 Servlet 2.5 的区分 :
	Servlet 3.0 : 较`Servlet 2.0`不需要在web.xml中配置Servlet映射,但需在Servlet类的定义处上编写注解: @WenServlet("/Servlet url..")其中"/Servlet url.."与Servlet 2.0在web.xml配置文件中<url-pattern>标签中的内容原理是相同的.

2.Servlet3.0 响应请求的路线 :
	如: 当点击`HiServlet.jsp`中的url链接时其先与@WebServlet中的值进行匹配,如果匹配成功,则说明请求的就是该注解所对应的类.
 */
@WebServlet("/ServletTest")
public class ServletTest extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public ServletTest() {
        super();
        // TODO Auto-generated constructor stub
    }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//response.getWriter().append("Served at: ").append(request.getContextPath());
		System.out.println("This is doGet ..");
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		// TODO Auto-generated method stub
		//doGet(request, response);
		System.out.println("This is doPost ..");
	}
}

/*#The result be shown as followed :
 * 
 * Apr 05, 2019 12:06:08 PM org.apache.catalina.startup.Catalina start 
 * INFO: Server startup in [3,425] milliseconds
 * This is doGet ..				    <— 点击`url`链接时`Servlet`触发的响应.
 * This is doPost ..	       		<— 点击`doPost`按钮时`Servlet`触发的响应.
 * This is doGet ..				    <— 点击`doGet`按钮时`Servlet`触发的响应.
 *
 */
```

- *JSP Program*
```html
<%@ page language="java" contentType="text/html; charset=ISO-8859-1" pageEncoding="ISO-8859-1"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="ISO-8859-1">
		<title>Servlet 3.0</title>
	</head>
	<body>
		<a href="ServletTest">doGet : Servlet 3.0</a>
		<div>
			<form action="ServletTest" method="get"><hr>
				<input type="submit" value="doGet " />
			</form>
			<form action="ServletTest" method="post"><br>
				<input type="submit" value="doPost" />
			</form>
		</div>
	</body>
</html>
```

- *web.xml*
```xml
<!-- 无需任何修改 -->
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://xmlns.jcp.org/xml/ns/javaee" xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd" id="WebApp_ID" version="4.0">
  <display-name>Servlet_basic_version_3.0</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
</web-app>
```

- *`JSP`运行效果示例图*
![ ](Java-Servlet各版本使用详解/Servlet2.5.PNG)


- **常被问到 : 辉仔啊,都`大二`下学期了呀,怎还是没有女朋友哟 ! 心想 : 是不是太爱`Coding`了呢 (灬°ω°灬) ~**