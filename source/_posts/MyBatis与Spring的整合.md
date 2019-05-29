---
title: MyBatis与Spring的整合
date: 2019-05-22 21:23:28
tags: [Spring,MyBatis]
---

## 学习笔记 : Spring与MyBatis整合

### 传统DAO方式的开发整合
*简介 : 采用传统DAO开发方式进行MyBatis与Spring框架的整合时,需要编写DAO接口以及接口的实现类,`并且需要向DAO实现类中注入SqlSessionFactory`,然后通过SqlSessionFactory创建SqlSession. 下面通过一个使用客户id查询数据表中客户信息的案例来演示该开发方式.*

1. *customer.sql : 客户信息表*
```sql
# 客户信息表
create table t_customer (

	id INT(20) PRIMARY KEY AUTO_INCREMENT,
	name VARCHAR(10) NOT NULL,
	job VARCHAR(10) NOT NULL,
	phone VARCHAR(12) NOT NULL
	
)
```

2. *db.properties : 数据库配置文件*
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

3. *applicationContext.xml : Spring核心配置文件*
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
		<!-- 指定核心配置文件位置 -->
		<property name="configLocation" value="classpath:mybatis-config.xml"/>
	</bean>
	
	<!-- 实例化Dao: 为SqlSessionDaoSupport类的子类对象注入一个SqlSessionFactory	-->
	<bean id="customerDao" class="pers.huangyuhui.sm.dao.impl.CustomerDaoImpl">
		<property name="sqlSessionFactory" ref="sqlSessionFactory"/>
	</bean>

</beans>
```

4. *mybatis-config.xml : MyBaits核心配置文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
	<!-- 配置别名 -->
	<typeAliases>
		<!-- 通过扫描包的形式定义别名: mybatis会将所有该包中的POJO类以首字母小写的非限定类名来作为它的别名 -->
		<package name="pers.huangyuhui.sm.po" />
	</typeAliases>

	<!-- 配置Mapper配置 -->
	<mappers>
		<mapper resource="pers/huangyuhui/sm/po/CustomerMapper.xml" />
	</mappers>
</configuration>
```

5. *Customer.java : 持久层*
```java
package pers.huangyuhui.sm.po;

/**
 * @ClassName: Customer
 * @Description: 客户信息
 * @author: HuangYuhui
 * @date: May 24, 2019 6:38:18 PM
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

6. *CustomerMapper.xml : 映射文件*
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="pers.huangyuhui.sm.po.CustomerMapper">
	<!-- 根据id查询客户信息,其中`customer`为Customer类的别名 -->
	<select id="findCustomerById" parameterType="Integer"  resultType="customer">
		select id,name,job,phone from t_customer where id = #{id}
	</select>
</mapper>
```

7. *CustomerDao.java : DAO层*
```java
package pers.huangyuhui.sm.dao;

import pers.huangyuhui.sm.po.Customer;

/**
 * @ClassName: CustomerDao
 * @Description: TODO
 * @author: HuangYuhui
 * @date: May 24, 2019 6:52:30 PM
 *
 */
public interface CustomerDao {

	/**
	 * @Title: findCustomerById
	 * @Description: 根据id查找客户信息
	 * @param: id
	 * @return: Customer
	 */
	public Customer findCustomerById(Integer id);
}
```

8. *CustomerDaoImpl.java : CustomerDao的实现类*
```java
package pers.huangyuhui.sm.dao.impl;

import org.mybatis.spring.support.SqlSessionDaoSupport;

import pers.huangyuhui.sm.dao.CustomerDao;
import pers.huangyuhui.sm.po.Customer;

/**
 * @ClassName: CustomerDaoImpl
 * @Description: 实现操作客户信息接口
 * @author: HuangYuhui
 * @date: May 24, 2019 6:54:24 PM
 */
//注意:SqlSessionDaoSupport类在使用时需要一个SqlSessionFactory或一个SqlSessionTemplate对象,所以需要通过
//Spring给SqlSessionDaoSupport类的子类对象注入一个SqlSessionFactory或SqlSessionTemplate.这样,在子类中就
//能够调用SqlSessionDaoSupport类的getSqlSession()方法来获取SqlSession对象,并使用SqlSession对象中的方法!
public class CustomerDaoImpl extends SqlSessionDaoSupport implements CustomerDao {

	private String mapperNamespace = "pers.huangyuhui.sm.po.CustomerMapper.";

	@Override
	// 通过id查找客户信息
	public Customer findCustomerById(Integer id) {
		// 调用SqlSessionDaoSupport类的getSqlSession()方法来获取SqlSession对象,并使用SqlSession对象中的方法!
		return this.getSqlSession().selectOne(mapperNamespace + "findCustomerById", id);
	}

}
```
*在此步骤需要在Spring配置文件applicationContext.xml中编写实例化CustomerDaoImpl的配置(`已添加`),代码如下所示 :*
```xml
<bean id="customerDao" class="pers.huangyuhui.sm.dao.impl.CustomerDaoImpl">
    <property name="sqlSessionFactory" ref="sqlSessionFactory"/>
</bean>
```

9. *SMTest.java : 整合测试类*
```java
package pers.huangyuhui.sm.test;

import org.junit.BeforeClass;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import pers.huangyuhui.sm.dao.CustomerDao;
import pers.huangyuhui.sm.po.Customer;

/**
 * @ClassName: SMTest
 * @Description: MyBatis+Spring整合测试
 * @author: HuangYuhui
 * @date: May 24, 2019 7:02:26 PM
 *
 */
public class SMTest {

	private static ApplicationContext applicationContext;

	@BeforeClass
	public static void init() {
		applicationContext = new ClassPathXmlApplicationContext("applicationContext.xml");
	}

	@Test
	public void findCustomerByIdTest() {
		// CustomerDao customerDao = (CustomerDao) applicationContext.getBean("customerDao");
		CustomerDao customerDao = (CustomerDao) applicationContext.getBean(CustomerDao.class);

		Customer customer = customerDao.findCustomerById(1);
		System.out.println(customer);
	}
}
```

10. *log4j.properties : 日志文件*
```t
#Global configuration
log4j.rootLogger=DEBUG,stdout
#Log configuration
log4j.logger.pers.huangyuhui.sm.test=TRACE
#Console configuration
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
log4j.appender.stdout.layout.layout.ConversionPattern=%5p [%t] - %m%n
```

11. *程序运行结果*
```
Fetching JDBC Connection from DataSource
JDBC Connection [1334618867, URL=xxxxxx, MySQL Connector/J] will not be managed by Spring

==>  Preparing: select id,name,job,phone from t_customer where id = ? 
==> Parameters: 1(Integer)
<==      Total: 1

Closing non transactional SqlSession [org.apache.ibatis.session.defaults.DefaultSqlSession@15c25153]
Returning JDBC Connection to DataSource
Customer [id=1, name=YUbuntu0109, job=student, phone=15111111111]
```



### Mapper接口方式的开发整合
. . .
