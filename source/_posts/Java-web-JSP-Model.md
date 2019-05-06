---
title: 'Java web:JSP Model'
date: 2019-05-05 22:11:44
tags: [Java web]
---

## 学习笔记 : Java web之JSP 开发模型

### JSP Model1
*简介 : `JSP Model1`采用JSP+JavaBean的技术,将页面显示和业务逻辑分开.其中,JSP实现流程控制和页面显示,JavaBean对象封装数据和业务逻辑.这种设计实现了数据,业务逻辑和页面显示的分离,在一定程度上实现了程序开发的模块化,降低了程序修改和维护的难度.*

#### JSP Model1 案例
*案例描述 : 通过一个简单的网络计算器程序来深化对JSP Model1模型的理解.*

1. *Calculator.java : 封装计算器中的数据,处理业务逻辑.*
```java
package pers.huangyuhui.jspmodel.model1.calculator;

import java.math.BigDecimal;
import java.util.HashMap;
import java.util.Map;
import java.util.regex.Pattern;

/**
 * @ClassName: Calculator
 * @Description: 封装计算器中的数据及处理业务逻辑
 * @author: HuangYuhui
 * @date: May 5, 2019 10:10:43 AM
 * 
 */
public class Calculator {

	private char operator;
	private String firstNumber;
	private String secondNumber;
	private Map<String, String> errors = new HashMap<String, String>();// 封装错误信息

	public String getFirstNumber() {
		return firstNumber;
	}

	public void setFirstNumber(String firstNumber) {
		this.firstNumber = firstNumber;
	}

	public String getSecondNumber() {
		return secondNumber;
	}

	public void setSecondNumber(String secondNumber) {
		this.secondNumber = secondNumber;
	}

	public char getOperator() {
		return operator;
	}

	public void setOperator(char operator) {
		this.operator = operator;
	}

	public Map<String, String> getErrors() {
		return errors;
	}

	public void setErrors(Map<String, String> errors) {
		this.errors = errors;
	}

	// 运算操作
	public String caculate() {
		BigDecimal result = null;
		BigDecimal firstNum = new BigDecimal(firstNumber);
		BigDecimal secondNum = new BigDecimal(secondNumber);

		switch (operator) {
		case '+':
			result = firstNum.add(secondNum);
			break;
		case '-':
			result = firstNum.subtract(secondNum);
			break;
		case '*':
			result = firstNum.multiply(secondNum);
			break;
		case '/':
			if ("0".equals(secondNumber)) {
				// throw new RuntimeException("除数不能为 0 !");
				return null;
			}
			result = firstNum.divide(secondNum);
			break;

		default:
			break;
		}

		return result.toString();
	}

	// 验证表单传入的数据是否合法
	public boolean validate() {
		boolean flag = true;
		Pattern pattern = Pattern.compile("\\d+");// 匹配数字
		if (firstNumber == null || "".equals(firstNumber)) {
			errors.put("firstNumberErrorInfo", "第一个运算数不能为空 !");
			flag = false;
		} else if (!pattern.matcher(firstNumber).matches()) {
			errors.put("firstNumberErrorInfo", "第一个运算数必须为数字 !");
			flag = false;
		}

		if (secondNumber == null || "".equals(secondNumber)) {
			errors.put("secondNumberErrorInfo", "第二个运算数不能为空 !");
			flag = false;
		} else if (!pattern.matcher(secondNumber).matches()) {
			errors.put("secondNumberErrorInfo", "第二个运算数必须为数字 !");
			flag = false;
		} else if ("0".equals(secondNumber)) {
			errors.put("secondNumberErrorInfo", "被除数不能为 0 !");
			flag = false;
		}

		return flag;
	}

}
```

2. *calculator.jsp : 显示网络计算机的页面.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8" import="java.util.Map"%>
<jsp:useBean id="calculator" class="pers.huangyuhui.jspmodel.model1.calculator.Calculator"/>
<jsp:setProperty property="*" name="calculator"/>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>JSP Model 1</title>
		<style>
			span{
				font-size:13px;
				color:red;
			}
			#main{
				width:600px;
				margin-left: 425px;
			}
			#main tr{
				height:40px;
			}
			#main tr td{
				width: 50px;
			}
			#main tr td input{
				width: 200px;
			}
			.buttom {
				text-align: center;
				padding-top: 25px;
			}
			.buttom .calculate{
				margin-right: 65px;
			}
		</style>
	</head>
	<body>
		<h3 align="center">JSP Mode 1 : 计算器</h3><hr/>
			<form action="" method="post">
				<table id="main">
					<tr>
						<td>	第一个运算数</td>
						<td >
							<input type="text" name="firstNumber"/>
							<span>${errors.firstNumberErrorInfo }</span>
						</td>
					</tr>
					<tr>
						<td>	运算符</td>
						<td>
							<select name="operator" style="margin-left:65px">
								<option value="+">+</option>
								<option value="-">-</option>
								<option value="*">*</option>
								<option value="/">/</option>
							</select>
						</td>
					</tr>
					<tr>
						<td>第二个运算符</td>
						<td>
							<input type="text" name="secondNumber"/>
							<span>${errors.secondNumberErrorInfo }</span>
						</td>
					</tr>
					<tr>
						<td>运算结果: </td>
						<td>
							<%
								Map<String,String> errorInfo = calculator.getErrors();
								if(calculator.validate()){
									pageContext.getSession().removeAttribute("errors");
							%>
							<font color="green">
								<jsp:getProperty property="firstNumber" name="calculator"/>
								<jsp:getProperty property="operator" name="calculator"/>
								<jsp:getProperty property="secondNumber" name="calculator"/>
								= <%=calculator.caculate() %>
							<%
								}else{
									pageContext.getSession().setAttribute("errors",errorInfo);
								}
							%>
							</font>
						</td>
					</tr>
				</table>
				<div class="buttom">
					<input class="calculate" type="submit" value="计算"/>
					<input type="reset" value="重置"/>
				</div>		
			</form>
	</body>
</html>
```

3. *程序运行效果示例图.*
![ ](Java-web-JSP-Model/JSPModel1案例-计算器页面.PNG)


### JSP Model2
*简介 : `JSP Model2`架构模型采用JSP+Servlet+JavaBean技术,其将`JSP Model1`中JSP页面里的流程控制代码提取出来.封装到Servlet中,从而实现了整个程序页面显示,流程控制和业务逻辑的分离.实际上JSP Model2就是`MVC`设计模式,其中控制器(`Colltroller`)的角色由Servlet实现,视图(`View`)的角色由JSP实现,模型(`Model`)的角色由JavaBean实现.*

#### JSP Model2 案例
*案例描述 : 按照`JSP Model2`的模型思想编写一个用户注册的简单示例程序.*

1. *UserBean.java : 封装用户的注册信息.*
```java
package pers.huangyuhui.jspmodel.model2.register;

/**
 * @ClassName: UserBean
 * @Description: 封装用户注册信息
 * @author: HuangYuhui
 * @date: May 5, 2019 4:35:21 PM
 * 
 */
public class UserBean {

	private String name;
	private String password;
	private String email;

	public UserBean(String name, String password, String email) {
		super();
		this.name = name;
		this.password = password;
		this.email = email;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

}
```

2. *RegisterFormBean.java : 封装并效验注册表单信息的JavaBean.*
```java
package pers.huangyuhui.jspmodel.model2.register;

import java.util.HashMap;
import java.util.Map;

/**
 * @ClassName: RegisterFormBean
 * @Description: 效验注册表单信息
 * @author: HuangYuhui
 * @date: May 5, 2019 4:37:33 PM
 * 
 */
public class RegisterFormBean {

	private String name;
	private String password;
	private String password2;
	private String email;

	// 存储验证表单时发生的错误信息
	private Map<String, String> errors = new HashMap<String, String>();

	public RegisterFormBean(String name, String password, String password2, String email) {
		super();
		this.name = name;
		this.password = password;
		this.password2 = password2;
		this.email = email;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getPassword() {
		return password;
	}

	public void setPassword(String password) {
		this.password = password;
	}

	public String getPassword2() {
		return password2;
	}

	public void setPassword2(String password2) {
		this.password2 = password2;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	// 获取注册信息效验时发生的错误信息
	public Map<String, String> getErrors() {
		return errors;
	}

	// 添加注册信息效验时发生的错误信息
	public void setErrorsMsg(String error, String errorMsg) {
		if ((error) != null && (errorMsg != null)) {
			errors.put(error, errorMsg);
		}
	}

	// 校验用户注册信息
	public boolean validate() {
		boolean flag = true;

		if (name == null || name.trim().equals("")) {
			errors.put("nameErrorInfo", "请输入用户名 !");
			flag = false;
		}

		if (password == null || password.trim().equals("")) {
			errors.put("passwordErrorInfo", "请输入密码 !");
			flag = false;
		} else if (password.length() > 16 || password.length() < 6) {
			errors.put("passwordErrorInfo", "请输入6-12位的密码 !");
			flag = false;
		}

		if (password != null && !password.equals(password2)) {
			errors.put("password2ErrorInfo", "两次输入的密码不相同 !");
			flag = false;
		}

		if (email == null || email.trim().equals("")) {
			errors.put("emailErrorInfo", "请输入邮箱地址 !");
			flag = false;
		} else if (!email.matches("[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\\.[a-zA-Z0-9_-]+)+")) {
			errors.put("emailErrorInfo", "邮箱地址格式错误 !");
			flag = false;
		}

		return flag;
	}

}
```

3. *DBUtil.java : 访问数据库的辅助类,充当DAO.*
```java
package pers.huangyuhui.jspmodel.model2.register;

import java.util.HashMap;

/**
 * @ClassName: DBUtil
 * @Description: 模拟数据库,充当DAO
 * @author: HuangYuhui
 * @date: May 5, 2019 4:50:10 PM
 * 
 */
public class DBUtil {

	private static DBUtil instance = new DBUtil();
	private HashMap<String, UserBean> users = new HashMap<String, UserBean>();

	private DBUtil() {
		// 向数据库中添加一条用户数据
		UserBean firstUser = new UserBean("YUbuntu0109", "myPassword", "Gentleman_0109@outlook.com");
		users.put("YUbuntu0109", firstUser);
	}

	public static DBUtil getInstance() {
		return instance;
	}

	// 获取数据库中指定用户名的数据
	public UserBean getUser(String userName) {

		UserBean user = users.get(userName);
		return user;
	}

	// 向数据库中添加新的用户数据
	public boolean insertUser(UserBean user) {
		if (user == null) {
			return false;
		}

		String userName = user.getName();
		if (users.get(userName) != null) {
			return false;
		}
		users.put(userName, user);
		return true;
	}

}
```

4. *ControllerServlet.java : 控制器,负责处理用户注册的请求.*
```java
package pers.huangyuhui.jspmodel.model2.register;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @ClassName: ColltrollerServlet
 * @Description: 处理用户注册请求
 * @author: HuangYuhui
 * @date: May 5, 2019 5:01:46 PM
 *
 */
@WebServlet("/ControllerServlet")
public class ControllerServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;

	public ControllerServlet() {
		super();
	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {

		request.setCharacterEncoding("utf-8");
		response.setContentType("text/html;charset=utf-8");

		String name = request.getParameter("userName");
		String password = request.getParameter("userPassword");
		String password2 = request.getParameter("userPassword2");
		String email = request.getParameter("userEmail");

		// 效验用户提交的注册表单信息
		RegisterFormBean registerFormBean = new RegisterFormBean(name, password, password2, email);
		if (!registerFormBean.validate()) {
			request.setAttribute("registerFromBean", registerFormBean);
			request.getRequestDispatcher("/register.jsp").forward(request, response);
			return;
		}

		// 存储用户注册信息
		UserBean userBean = new UserBean(name, password2, email);
		if (!DBUtil.getInstance().insertUser(userBean)) {
			request.setAttribute("BDUtilErrorInfo", "抱歉! 你注册的用户名已存在啦 !");
			request.setAttribute("registerFromBean", registerFormBean);
			request.getRequestDispatcher("/register.jsp").forward(request, response);
			return;
		}

		request.getSession().setAttribute("userBean", userBean);
		response.getWriter().print("注册成功啦 ! 三秒后将自动跳转到用户注册信息的页面  !");
		response.setHeader("refresh", "3;url=registerInfo.jsp");

	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		doGet(request, response);
	}

}
```

5. *register.jsp : 用户注册页面.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>JSP Model 2</title>
		<style type="text/css">
			h3{
				text-align:center;
			}
			#main{
				width:600px;
				margin-left: 450px;
			}
			#main tr{
				height:40px;
			}
			#main tr td{
				width: 50px;
			}
			#main tr td input{
				width: 200px;
			}
			span{
				font-size:13px;
				color:red;
			}
			.buttom {
				text-align: center;
				padding-top: 25px;
			}
			.buttom .register{
				margin-right: 70px;
			}
		</style>
	</head>
	<body>
		<h3>用户注册页面</h3>
		<hr/>
			<form action="ControllerServlet" method="post">
				<table id="main">
					 <tr>
					 	<td>用户名</td>
					 	<td>
					 		<input type="text" name="userName" value="${registerFromBean.name }"/>
					 		<span>
					 			${registerFromBean.errors.nameErrorInfo }
					 			${BDUtilErrorInfo }
					 		</span>
					 	</td>
					 </tr>
					 <tr>
					 	<td>密码</td>
					 	<td>
					 		<input type="password" name="userPassword" />
					 		<span>
								${registerFromBean.errors.passwordErrorInfo }
					 		</span>
					 	</td>
					 </tr>
					 <tr>
					 	<td>确认密码</td>
					 	<td>
					 		<input type="password" name="userPassword2"/>
					 		<span>
					 			${registerFromBean.errors.password2ErrorInfo }
					 		</span>
					 	</td>
					 </tr>
					 <tr>
					 	<td>邮箱</td>
					 	<td>
					 		<input type="text" name="userEmail" value="${registerFromBean.email }"/>
					 		<span>
					 			${registerFromBean.errors.emailErrorInfo }
					 		</span>
					 	</td>
					 </tr>
				</table>
				<div class="buttom">
					<input class="register" type="submit" value="注册"/>
					<input type="reset" value="重置"/>
				</div>
			</form>
	</body>
</html>
```

6. *registerInfo.jsp : 用户注册成功页面.*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>JSP Model 2</title>
	</head>
	<body>
		<h3 align="center">JSP Mode2 : 用户注册信息</h3>
		<hr/>
			<c:choose>
				<c:when test="${sessionScope.userBean == null  }">
					<a href="${pageContext.request.contextPath }/register.jsp">你还未注册! 请注册哟ヾ(●´∀｀●) ~</a>
				</c:when>			
				<c:otherwise>
					<div align="center">
						<p>你的姓名: ${sessionScope.userBean.name }</p>
						<p>你的邮箱: ${sessionScope.userBean.email }</p>
					</div>
				</c:otherwise>
			</c:choose>
	</body>
</html>
```

7. *程序运行效果示例图.*
![ ](Java-web-JSP-Model/JSPModel2案例-用户注册页面.PNG)
![ ](Java-web-JSP-Model/JSPModel2案例-用户注册信息页面.PNG)