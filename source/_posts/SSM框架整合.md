---
title: SSM框架整合
date: 2019-05-23 22:05:49
tags: [Spring,Spring MVC,MyBatis]
---

## 学习笔记 : SSM整合

### 整合环境搭建
*简介 : 由于Spring MVC是Spring框架中的一个模块,所以Spring MVC与Spring之间不存在整合问题,因此SSM整合主要只涉及Spring与MyBatis的整合.下面通过客户id查询数据表中指定的客户信息的简单案例来演示SSM整合思路 ~*


### 编写配置文件
1. *db.properties : 数据库配置文件*
```t
#
#database configuration information
#
jdbc.driver=com.mysql.cj.jdbc.Driver
jdbc.url=jdbc:mysql:xxxxxx?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true
jdbc.username=xxxxxx
jdbc.password=xxxxxx
#
#DBCP configuration
#
jdbc.maxTotal=30    #最大连接数
jdbc.maxIdle=10     #最大空闲连接数
jdbc.initialSize=5  #初始化连接数
```

2. *applicationContext.xml : Spring核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:tx="http://www.springframework.org/schema/tx"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd">

	<!-- 读取db.properties -->
	<context:property-placeholder location="classpath:db.properties"/>
	
	<!-- 配置数据源 -->
	<bean id="dataSource" class="org.apache.commons.dbcp2.BasicDataSource">
		<!-- 数据库驱动 -->
		<property name="driverClassName" value="${jdbc.driver}"/>
		<!-- 连接数据库的url -->
		<property name="url" value="${jdbc.url}"/>
		<!-- 连接数据库的用户名 -->
		<property name="username" value="${jdbc.username}"/>
		<!-- 连接数据库的密码 -->
		<property name="password" value="${jdbc.password}"/>
		<!-- 最大连接数 -->
		<property name="maxTotal" value="${jdbc.maxTotal}"/>
		<!-- 最大空闲数 -->
		<property name="maxIdle" value="${jdbc.maxIdle}"/>
		<!-- 初始化连接数 -->
		<property name="initialSize" value="${jdbc.initialSize}"/>
	</bean>
	
	<!-- 事务管理器,依赖于数据源 -->
	<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"/>
	</bean>
	
	<!-- 开启事务注解 -->
	<tx:annotation-driven transaction-manager="transactionManager"/>
	
	<!-- 配置MyBatis工厂: 构建SqlSessionFactory -->
	<!-- 指定数据源和配置文件: 使得Spring IOC容器在初始化id为sqlSessionFactory的Bean时解析MyBatis的配置文件,并与数据源一同保存到Spring的Bean中 -->
	<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
		<!-- 注入数据源 -->
		<property name="dataSource" ref="dataSource"/>
		<!-- 指定MyBatis核心配置文件位置 -->
		<property name="configLocation" value="classpath:mybatis-config.xml"/>
	</bean>
	
	<!-- 配置mapper扫描器 -->
	<bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
		<property name="basePackage" value="pers.huangyuhui.ssm.dao"/>
	</bean>
	
	<!-- 扫描Service -->
	<context:component-scan base-package="pers.huangyuhui.ssm.service"/>
	
</beans>
```

3. *mybatis-config.xml : MyBatis核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<!-- 配置别名 -->
	<typeAliases>
		<!-- 通过扫描包的形式定义别名: mybatis会将所有该包中的POJO类以首字母小写的非限定类名来作为它的别名 -->
		<package name="pers.huangyuhui.ssm.po" />
	</typeAliases>

	<!-- 配置Mapper -->
	<mappers>
		<!-- 使用Mapper接口动态代理开发时,如果完全遵循了编写规范,那么该配置文件中无需以下映射文件 -->
        <!-- <mapper resource="pers/huangyuhui/ssm/dao/CustomerDao.xml"/> -->
	</mappers>
</configuration>
```

4. *web.xml*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
	id="WebApp_ID" version="4.0">

	<display-name>ssm_01</display-name>
	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>

	<!-- 配置加载Spring文件的监听器 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext.xml</param-value>
	</context-param>
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<!-- 编码过滤器 -->
	<filter>
		<filter-name>encoding</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>
	<filter-mapping>
		<filter-name>encoding</filter-name>
		<url-pattern>*.action</url-pattern>
	</filter-mapping>

	<!-- 配置Spring MVC前端核心过滤器 -->
	<servlet>
		<servlet-name>spring_mvc</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>classpath:springmvc-config.xml</param-value>
		</init-param>
		<!-- 配置服务器启动后立即加载Spring MVC配置文件 -->
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>spring_mvc</servlet-name>
		<!-- 拦截除了jsp外的所有请求 -->
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	
	<!-- 
	Spring-context.jar包中提供了通过JMX来实时查看Spring放在applicationContext里的bean列表功能,
	其会在项目配置文件里找一个叫`spring.liveBeansView.mbeanDomain`的环境变量,如果没找到,
	就会抛出异常:[spring.liveBeansView.mbeanDomain] threw NamingException ...
	解决方案: 在项目中的web.xml中添加如下配置
	-->
	<context-param>
		<param-name>spring.profiles.active</param-name>
		<param-value>dev</param-value>
	</context-param>
	<context-param>
		<param-name>spring.profiles.default</param-name>
		<param-value>dev</param-value>
	</context-param>
	<context-param>
		<param-name>spring.liveBeansView.mbeanDomain</param-name>
		<param-value>dev</param-value>
	</context-param>

</web-app>
```



### 整合应用测试
*上述已经了SSM框架整合环境的搭建工作,接下来开始整合开发工作 ~*
1. *t_customer.sql : 客户信息表*
```sql
create table t_customer (

	id INT(20) PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(10) NOT NULL,
	job VARCHAR(10) NOT NULL,
	phone VARCHAR(12) NOT NULL
	
)
```

2. *Customer.java : 持久化类*
```java
package pers.huangyuhui.ssm.po;

/**
 * @ClassName: Customer
 * @Description: 客户信息
 * @author: HuangYuhui
 * @date: May 25, 2019 11:07:37 AM
 *
 */
public class Customer {

	private Integer id;
	private String name;
	private String job;
	private String phone;

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getJob() {
		return job;
	}

	public void setJob(String job) {
		this.job = job;
	}

	public String getPhone() {
		return phone;
	}

	public void setPhone(String phone) {
		this.phone = phone;
	}

	@Override
	public String toString() {
		return "Customer [id=" + id + ", name=" + name + ", job=" + job + ", phone=" + phone + "]";
	}

}
```

3. *CustomerDao.java : Dao层*
```java
package pers.huangyuhui.ssm.dao;

import pers.huangyuhui.ssm.po.Customer;

/**
 * @ClassName: CustomerDao
 * @Description: TODO
 * @author: HuangYuhui
 * @date: May 25, 2019 11:09:22 AM
 *
 */
public interface CustomerDao {

	/**
	 * @Title: findCustomerById
	 * @Description: 根据客户id查找客户信息
	 * @param: id
	 * @return: Customer
	 */
	public Customer findCustomerById(Integer id);
}
```

4. *CustomerDao.xml : 接口对应的映射文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="pers.huangyuhui.ssm.dao.CustomerDao">
	<!-- 根据客户id查找客户信息 -->
	<select id="findCustomerById" parameterType="Integer" resultType="customer">
		select id,name,job,phone from t_customer where id = #{id}
	</select>

</mapper>
```

5. *CustomerService.java : Service层*
```java
package pers.huangyuhui.ssm.service;

import pers.huangyuhui.ssm.po.Customer;

/**
 * @ClassName: CustomerService
 * @Description: TODO
 * @author: HuangYuhui
 * @date: May 25, 2019 11:15:26 AM
 *
 */
public interface CustomerService {

	/**
	 * 
	 * @Title: findCustomerById
	 * @Description: 通过id查找客户信息
	 * @param: id
	 * @return: Customer
	 */
	public Customer findCustomerById(Integer id);
}
```

6. *CustomerServiceImpl.java : CustomerService的实现类*
```java
package pers.huangyuhui.ssm.service.impl;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import pers.huangyuhui.ssm.dao.CustomerDao;
import pers.huangyuhui.ssm.po.Customer;
import pers.huangyuhui.ssm.service.CustomerService;

/**
 * @ClassName: CustomerServiceImpl
 * @Description: CustomerService的实现类
 * @author: HuangYuhui
 * @date: May 25, 2019 11:17:12 AM
 *
 */

@Service // 标识为业务层的实现类
@Transactional // 将类中所有方法都纳入Spring的事物管理
public class CustomerServiceImpl implements CustomerService {

	@Autowired // 将CustomerDao接口对象注入到本类
	private CustomerDao customerDao;

	@Override
	// 查询客户信息
	public Customer findCustomerById(Integer id) {
		return this.customerDao.findCustomerById(id);
	}

}
```

7. *CustomerController.java : 控制器*
```java
package pers.huangyuhui.ssm.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestMapping;

import pers.huangyuhui.ssm.po.Customer;
import pers.huangyuhui.ssm.service.CustomerService;

/**
 * @ClassName: CustomerController
 * @Description: 用于处理页面请求的控制器
 * @author: HuangYuhui
 * @date: May 25, 2019 11:21:39 AM
 *
 */

@Controller // 标识为控制器
public class CustomerController {

	@Autowired // 将CustomerService接口对象注入到本类
	private CustomerService customerService;

	/**
	 * @Title: findCustomerById
	 * @Description: 通过id查询客户信息
	 * @param: id
	 * @param: model
	 * @return: String
	 */
	@RequestMapping("/findCustomerById")
	public String findCustomerById(Integer id, Model model) {
		Customer customer = customerService.findCustomerById(id);
		model.addAttribute("customer", customer);

		// 返回客户信息展示页面
		return "customerInfo";
	}
}
```

8. *WebContent/WEB-INF/`view/customerInfo.jsp` : 展示客户信息页面*
```html
<%@ page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>SSM整合案例</title>
</head>
<body>
	<h2 align="center">客户信息</h2><hr/>
	<table align="center" border="1" >
		<tr>
			<td>编号</td>
			<td>姓名</td>
			<td>职业</td>
			<td>电话</td>
		</tr>
		<tr>
			<td>${customer.id }</td>
			<td>${customer.name }</td>
			<td>${customer.job }</td>
			<td>${customer.phone }</td>
	</table>
</body>
</html>
```

9. *log4j.properties : 日志文件*
```t
#Global configuration
log4j.rootLogger=DEBUG,stdout
#Log configuration
log4j.logger.pers.huangyuhui.ssm.controller=TRACE
#Console configuration
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.layout.ConversionPattern=%5p [%t] - %m%n
```

9.  *将项目发布到Tomcat并启动,在浏览器中访问地址: `http://localhost:8080/ssm_01/findCustomerById?id=1`,其页面显示效果如下图所示.*
    
![ ](SSM框架整合/SSM整合案例.PNG)
*由图可知,通过浏览器成功查出了`t_customer`表中id为1的客户信息.继而证明`SSM`框架整合成功!ヾ(◍°∇°◍)ﾉﾞ*


10.  *Console输出的主要日志信息如下 :*
```
DispatcherServlet with name 'spring_mvc' processing GET request for [/ssm_01/findCustomerById]

Looking up handler method for path /findCustomerById

Returning handler method [public java.lang.String pers.huangyuhui.ssm.controller.CustomerController.findCustomerById(java.lang.Integer,org.springframework.ui.Model)]

Returning cached instance of singleton bean 'customerController'

Last-Modified value for [/ssm_01/findCustomerById] is: -1

Creating new transaction with name [pers.huangyuhui.ssm.service.impl.CustomerServiceImpl.findCustomerById]: PROPAGATION_REQUIRED,ISOLATION_DEFAULT; ''

Acquired Connection [636338281, URL=jdbc:mysql://LOCALHOST/mybatis_spring?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true, UserName=root@localhost, MySQL Connector/J] for JDBC transaction

Switching JDBC Connection [636338281, URL=jdbc:mysql://LOCALHOST/mybatis_spring?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true, UserName=root@localhost, MySQL Connector/J] to manual commit

Creating a new SqlSession

Registering transaction synchronization for SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@61b17833]

JDBC Connection [636338281, URL=jdbc:mysql://LOCALHOST/mybatis_spring?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true, UserName=root@localhost, MySQL Connector/J] will be managed by Spring

==>  Preparing: select id,name,job,phone from t_customer where id = ? 
==> Parameters: 2(Integer)
<==      Total: 1

Releasing transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@61b17833]

Transaction synchronization committing SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@61b17833]
Transaction synchronization deregistering SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@61b17833]
Transaction synchronization closing SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@61b17833]

Initiating transaction commit

Committing JDBC transaction on Connection [636338281, URL=jdbc:mysql://LOCALHOST/mybatis_spring?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true, UserName=root@localhost, MySQL Connector/J]

Releasing JDBC Connection [636338281, URL=jdbc:mysql://LOCALHOST/mybatis_spring?useSSL=false&serverTimezone=UTC&allowPublicKeyRetrieval=true, UserName=root@localhost, MySQL Connector/J] after transaction

Returning JDBC Connection to DataSource

Invoking afterPropertiesSet() on bean with name 'customerInfo'

Rendering view [org.springframework.web.servlet.view.InternalResourceView: name 'customerInfo'; URL [/WEB-INF/view/customerInfo.jsp]] in DispatcherServlet with name 'spring_mvc'

Added model object 'customer' of type [pers.huangyuhui.ssm.po.Customer] to request in view with name 'customerInfo'

Added model object 'org.springframework.validation.BindingResult.customer' of type [org.springframework.validation.BeanPropertyBindingResult] to request in view with name 'customerInfo'

Forwarding to resource [/WEB-INF/view/customerInfo.jsp] in InternalResourceView 'customerInfo'

Successfully completed request
```